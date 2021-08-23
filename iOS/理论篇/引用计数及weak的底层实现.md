## oc中的引用计数存放在哪？

可能在isa中也可能在SideTable中。

在isa中有一个结构体，结构体中有一个extra_rc用来存放引用计数。该结构体中还存在has_sidetable_rc字段，当该字段为1的时候引用计数就存储在了sidetable中。

## SideTables

SideTables 可以理解为全局的hash表，里面存放 SideTable 类型的数据，其长度为 64.

## SideTable

主要存放 oc 对象的引用计数和弱引用相关信息。

```c
struct SideTable {
    spinlock_t slock;           // 自旋锁，防止多线程访问冲突
    RefcountMap refcnts;        // 对象引用计数map
    weak_table_t weak_table;    // 对象弱引用map

    SideTable() {
        memset(&weak_table, 0, sizeof(weak_table));
    }

    ~SideTable() {
        _objc_fatal("Do not delete SideTable.");
    }

    // 锁操作 符合StripedMap对T的定义
    void lock() { slock.lock(); }
    void unlock() { slock.unlock(); }
    void forceReset() { slock.forceReset(); }

    // Address-ordered lock discipline for a pair of side tables.

    template<HaveOld, HaveNew>
    static void lockTwo(SideTable *lock1, SideTable *lock2);
    template<HaveOld, HaveNew>
    static void unlockTwo(SideTable *lock1, SideTable *lock2);
};
```

`RefcountMap refcnts` 用来存储OC对象的引用计数。它实质上是一个以 `objc_object` 为key的hash表，其vaule就是OC对象的引用计数。同时，当OC对象的引用计数变为0时，会自动将相关的信息从hash表中剔除。

`weak_table_t weak_table` 存储对象弱引用指针的hash表。是OC weak功能实现的核心数据结构。

## weak_table_t

```c
/**
 * The global weak references table. Stores object ids as keys,
 * and weak_entry_t structs as their values.
 */
struct weak_table_t {
    weak_entry_t *weak_entries;        // hash数组，用来存储弱引用对象的相关信息weak_entry_t
    size_t    num_entries;             // hash数组中的元素个数
    uintptr_t mask;                    // hash数组长度-1，会参与hash计算。（注意，这里是hash数组的长度，而不是元素个数。比如，数组长度可能是64，而元素个数仅存了2个）
    uintptr_t max_hash_displacement;   // 可能会发生的hash冲突的最大次数，用于判断是否出现了逻辑错误（hash表中的冲突次数绝不会超过改值）
};
```

`weak_table_t` 是一个典型的 `hash` 结构。其中 `weak_entry_t *weak_entries` 是一个动态数组，用来存储 `weak_table_t` 的数据元素 `weak_entry_t`。

## weak_entry_t

`weak_table_t` 中存储的元素是 `weak_entry_t` 类型，每个 `weak_entry_t` 类型对应了一个OC对象的弱引用信息。

```c
/**
 * The internal structure stored in the weak references table. 
 * It maintains and stores
 * a hash set of weak references pointing to an object.
 * If out_of_line_ness != REFERRERS_OUT_OF_LINE then the set
 * is instead a small inline array.
 */
#define WEAK_INLINE_COUNT 4

// out_of_line_ness field overlaps with the low two bits of inline_referrers[1].
// inline_referrers[1] is a DisguisedPtr of a pointer-aligned address.
// The low two bits of a pointer-aligned DisguisedPtr will always be 0b00
// (disguised nil or 0x80..00) or 0b11 (any other address).
// Therefore out_of_line_ness == 0b10 is used to mark the out-of-line state.
#define REFERRERS_OUT_OF_LINE 2

struct weak_entry_t {
    DisguisedPtr<objc_object> referent; // 被弱引用的对象
    
    // 引用该对象的对象列表，联合。 引用个数小于4，用inline_referrers数组。 用个数大于4，用动态数组weak_referrer_t *referrers
    union {
        struct {
            weak_referrer_t *referrers;                      // 弱引用该对象的对象指针地址的hash数组
            uintptr_t        out_of_line_ness : 2;           // 是否使用动态hash数组标记位
            uintptr_t        num_refs : PTR_MINUS_2;         // hash数组中的元素个数
            uintptr_t        mask;                           // hash数组长度-1，会参与hash计算。（注意，这里是hash数组的长度，而不是元素个数。比如，数组长度可能是64，而元素个数仅存了2个）素个数）。
            uintptr_t        max_hash_displacement;          // 可能会发生的hash冲突的最大次数，用于判断是否出现了逻辑错误（hash表中的冲突次数绝不会超过改值）
        };
        struct {
            // out_of_line_ness field is low bits of inline_referrers[1]
            weak_referrer_t  inline_referrers[WEAK_INLINE_COUNT];
        };
    };

    bool out_of_line() {
        return (out_of_line_ness == REFERRERS_OUT_OF_LINE);
    }

    weak_entry_t& operator=(const weak_entry_t& other) {
        memcpy(this, &other, sizeof(other));
        return *this;
    }

    weak_entry_t(objc_object *newReferent, objc_object **newReferrer)
        : referent(newReferent) // 构造方法，里面初始化了静态数组
    {
        inline_referrers[0] = newReferrer;
        for (int i = 1; i < WEAK_INLINE_COUNT; i++) {
            inline_referrers[i] = nil;
        }
    }
};
```
