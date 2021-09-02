# block详解

## block是什么？

block 本质上也是一个 OC 对象，它内部含有一个 isa 指针。

block 是封装了函数调用以及函数调用环境的对象。

## block的变量捕获

为了保证 block 能正常访问外部变量，block 有一个变量捕获机制。

### 局部变量

#### 1. auto 变量

auto 自动变量，离开作用域就会自动销毁，局部变量前面默认有一个 auto 关键字修饰。自动变量会被捕获到 block 内部，也就是说 block 内部会新增一个参数来存储变量的值。

auto 只存在与局部变量中，访问方式为值传递。

#### 2. static 变量

static 修饰的变量为指针传递，它也会被block捕获。

### 全局变量

全局变量直接访问，不需要捕获。

### 总结

| 变量类型         | 捕获到 block 内部 | 访问方式 |
| ---------------- | ----------------- | -------- |
| 局部变量(auto)   | YES               | 值传递   |
| 局部变量(static) | YES               | 指针传递 |
| 全局变量         | NO                | 直接访问 |

## block 的类型

### 全局 block

block 内部没有访问 auto 变量时，block 为全局 block，存放在内存区域的数据段上。

### 栈 block

block 内部访问了 auto 变量，block 为栈 block，存放在内存区域的栈区。

### 堆 block

栈 block 调用了 copy，则会将栈 block 拷贝到堆区，变成堆 block。

|    block 类型     |            环境            | 内存区域 |               调用copy效果                |
| :---------------: | :------------------------: | :------: | :---------------------------------------: |
| __NSGlobalBlock__ |      没有访问auto变量      |  数据段  |          什么都不做，不改变类型           |
| __NSStackBlock__  |       访问了auto变量       |    栈    | 从栈赋值到堆，改变类型为__NSMallocBlock__ |
| __NSMallocBlock__ | __NSStackBlock__调用了copy |    堆    |         引用计数加一，不改变类型          |

## 什么时候栈上的block会被复制到堆呢？

### 1. 调用block的copy函数时

```objc
int a = 10;
// 调用auto变量，所以为__NSStackBlock__
// 再调用copy，将block从栈内存赋值到堆内存上
NSLog(@"%@", [^{
    NSLog(@"%d", a);
} copy]);
```

### 2. block作为函数返回值时

```objc
typedef void (^Block)(void);
Block myblock()
{
    int a = 10;
    // 上文提到过，block中访问了auto变量，此时block类型应为__NSStackBlock__
    Block block = ^{
        NSLog(@"---------%d", a);
    };
    return block;
}
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Block block = myblock();
        block();
       // 打印block类型为 __NSMallocBlock__
        NSLog(@"%@",[block class]);
    }
    return 0;
}
```

### 3. 将block赋值给附有__strong修饰符id类型的类或者Block类型成员变量时

```objc
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // block内没有访问auto变量
        Block block = ^{
            NSLog(@"block---------");
        };
        NSLog(@"%@",[block class]);
        int a = 10;
        // block内访问了auto变量，但没有赋值给__strong指针
        NSLog(@"%@",[^{
            NSLog(@"block1---------%d", a);
        } class]);
        // block赋值给__strong指针
        Block block2 = ^{
          NSLog(@"block2---------%d", a);
        };
        NSLog(@"%@",[block1 class]);
    }
    return 0;
}
```

### 4. 方法中含有usingBlock的Cocoa框架方法或者GCD的API中传递Block时

```objc
NSArray *array = @[];
[array enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
            
}];
```

```objc
static dispatch_once_t onceToken;
dispatch_once(&onceToken, ^{
            
});        
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            
});
```

### 总结

在 ARC 中，捕获外部了变量的 block 的类会是 `__NSMallocBlock__` 或者 `__NSStackBlock__`，如果 block 被赋值给了某个变量在这个过程中会执行 _Block_copy 将原有的 `__NSStackBlock__` 变成 `__NSMallocBlock__`；但是如果 block 没有被赋值给某个变量，那它的类型就是 `__NSStackBlock__`；没有捕获外部变量的 block 的类会是 `__NSGlobalBlock__` 即不在堆上，也不在栈上，它类似 C 语言函数一样会在代码段中。
在非 ARC 中，捕获了外部变量的 block 的类会是 `__NSStackBlock__`，放置在栈上，没有捕获外部变量的 block 时与 ARC 环境下情况相同。