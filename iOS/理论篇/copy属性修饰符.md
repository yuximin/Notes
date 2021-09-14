# copy 属性修饰符

> 本文可与 [关于iOS中copy和mutableCopy的理解](./关于iOS中copy和mutableCopy的理解.md) 一文同步阅读。

将对象赋值给 copy 修饰的属性时，会自动对对象执行一次 copy 操作，并将操作结果赋值给属性。**需要注意的是，该 copy 操作是在 setter 方法中调用的。** 所以，只有通过 `self.xxx = ***` 的方式给属性赋值时有效，直接给属性对应的成员变量赋值时无效。等效于如下代码：

```objective-c
- (void)setProperty:(NSString *)value {
  [_property release];
  _property = [value copy];
}
```

下面分别对可能出现的几种情况进行讨论。

## 1. 不可变类型对象赋值给 copy 修饰的不可变类型属性

```objective-c
@property (nonatomic, copy) NSArray *array;

NSArray *originArray = [[NSArray alloc] initWithObjects:@"1", @"2", @"3", @"4", nil];
self.array = originArray;
// 1
```

打印结果如下：

```
// 1
po originArray
<__NSArrayI 0x60000242ce40>(
1,
2,
3,
4
)

po _array
<__NSArrayI 0x60000242ce40>(
1,
2,
3,
4
)
```

可见，将不可变类型对象赋值给 copy 修饰的不可变类型属性时，执行的是浅拷贝。此时属性为不可变类型，指向的还是对象原本的内存地址。

## 2. 可变类型对象赋值给 copy 修饰的不可变类型属性

```objective-c
@property (nonatomic, copy) NSArray *array;

NSMutableArray *originMutableArray = [NSMutableArray arrayWithObjects:@"a", @"b", @"c", @"d", nil];
self.array = originMutableArray;
// 1
[originMutableArray removeLastObject];
// 2
originMutableArray[0] = @"1";
// 3
```

打印结果如下：

```
// 1
po originMutableArray
<__NSArrayM 0x600000ef1740>(
a,
b,
c,
d
)

po _array
<__NSArrayI 0x600000ef1320>(
a,
b,
c,
d
)

// 2
po originMutableArray
<__NSArrayM 0x600000ef1740>(
a,
b,
c
)

po _array
<__NSArrayI 0x600000ef1320>(
a,
b,
c,
d
)

// 3
po originMutableArray
<__NSArrayM 0x6000034d70f0>(
1,
b,
c
)

po _array
<__NSArrayI 0x6000034d6f40>(
a,
b,
c,
d
)
```

可见，将可变类型对象赋值给 copy 修饰的不可变类型属性时，执行的是深拷贝。会将原先可变类型对象的内容复制到一块新的内存地址，并返回一个不可变类型的对象赋值给当前属性。此时，属性和对象在内存上是相互独立的，无论是移除还是修改对象的值都不会对属性产生影响。

## 3. 不可变类型对象赋值给 copy 修饰的可变类型属性

```objective-c
@property (nonatomic, copy) NSMutableArray *mutableArray;

NSArray *originArray = [[NSArray alloc] initWithObjects:@"1", @"2", @"3", @"4", nil];
self.mutableArray = originArray;
// 1
[originMutableArray removeLastObject];
// 2
originMutableArray[0] = @"a";
// 3
```

打印结果如下：

```
// 1
po originArray
<__NSArrayI 0x600003063c30>(
1,
2,
3,
4
)

po _mutableArray
<__NSArrayI 0x600003063c30>(
1,
2,
3,
4
)
```

首先，在编译期间，第 4 行代码 `self.mutableArray = originArray;` 会产生警告，提示不能将 `NSArray` 赋值给 `NSMutableArray` ，指针类型不兼容。但赋值操作仍能正常运行。当执行第 6 行和第 8 行代码时，均会报错导致程序终止。这里贴出第 6 行代码执行的报错信息，提示找不到对应的方法。

```
-[__NSArrayI removeLastObject]: unrecognized selector sent to instance 0x600003063c30
*** Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: '-[__NSArrayI removeLastObject]: unrecognized selector sent to instance 0x600003063c30' ***
```

查看 1 断点处打印信息，可见，将不可变类型对象赋值给 copy 修饰的可变类型属性时，执行的是浅拷贝。会将不可变类型对象的地址返回给当前属性，当前可变类型属性的内容实际上是一个不可变类型的对象。所以，在执行后续方法时才会产生报错。

## 4. 可变类型对象赋值给 copy 修饰的可变类型属性

```objective-c
@property (nonatomic, copy) NSMutableArray *mutableArray;

NSMutableArray *originMutableArray = [NSMutableArray arrayWithObjects:@"a", @"b", @"c", @"d", nil];
self.mutableArray = originMutableArray;
// 1
[originMutableArray removeLastObject];
// 2
originMutableArray[0] = @"1";
//  3
```

打印结果如下：

```
// 1
po originMutableArray
<__NSArrayM 0x6000000a5d10>(
a,
b,
c,
d
)

po _mutableArray
<__NSArrayI 0x6000000a4fc0>(
a,
b,
c,
d
)

// 2
po originMutableArray
<__NSArrayM 0x6000000a5d10>(
a,
b,
c
)

po _mutableArray
<__NSArrayI 0x6000000a4fc0>(
a,
b,
c,
d
)

// 3
po originMutableArray
<__NSArrayM 0x6000000a5d10>(
1,
b,
c
)

po _mutableArray
<__NSArrayI 0x6000000a4fc0>(
a,
b,
c,
d
)
```

可见，将可变类型对象赋值给 copy 修饰的可变类型属性时，执行的是深拷贝。对原先的可变对象执行 copy 操作，返回一个不可变类型的对象并赋值给当前属性。此时，属性和原对象是相互独立的，原对象的修改不会作用到当前属性上。另外，当前属性的内容实际上是一个不可变类型对象，若执行修改操作，则会发生报错，效果同上面 3 小节所述。

## 总结

由上述分类讨论可得出结论，copy 修饰的属性，在被赋值时，会自动对赋值对象执行一次 copy 操作，并将操作的结果返回给当前属性。所以，赋值过程中发生的是深拷贝还是浅拷贝，是由赋值对象的类型决定的。若赋值对象为不可变类型，则为浅拷贝；若赋值对象为可变类型，则为深拷贝，同时生成一个不可变类型对象赋值给属性。另外，无论属性是可变类型还是不可变类型，被 copy 修饰后，通过 setter 方法赋值的结果都是一个不可变类型的对象。

|                                | 不可变 --> 不可变 copy 属性 | 可变 --> 不可变 copy 属性 | 不可变 --> 可变 copy 属性 | 可变 --> 可变 copy 属性 |
| ------------------------------ | --------------------------- | ------------------------- | ------------------------- | ----------------------- |
| 赋值类型                       | 浅拷贝                      | 深拷贝                    | 浅拷贝                    | 深拷贝                  |
| 赋值后属性类型                 | 不可变类型                  | 不可变类型                | 不可变类型                | 不可变类型              |
| 是否产生新对象（新的内存地址） | 否                          | 是                        | 否                        | 是                      |

## 番外：NSString 为什么用 copy 修饰？

考虑一种情况，将 NSMutableString 对象赋值给 NSString 属性时，代码如下：

```objective-c
@property (nonatomic, strong) NSString *str;
/// @property (nonatomic, copy) NSString *str;

NSMutableString *mutableStr = [NSMutableString stringWithString:@"abcdefg"];
self.str = mutableStr;
// 1
[mutableStr appendString:@"123"];
// 2
```

### 使用 strong 修饰

打印结果如下：

```
// 1
po mutableStr
abcdefg

po _str
abcdefg

// 2
po mutableStr
abcdefg123

po _str
abcdefg123
```

从打印信息可以看出，将 NSMtableString 对象赋值给 NSString 属性时，程序并未报任何警告和报错。此时，属性值和对象值一致。然后，我们向 NSMutableString 对象追加字符串时，可以看到该操作同样作用于 NSString 属性。按照常识，NSString 的内容是不可被修改的，但此处却被修改了。

### 使用 copy 修饰

打印结果如下：

```
// 1
po mutableStr
abcdefg

po _str
abcdefg

// 2
po mutableStr
abcdefg123

po _str
abcdefg
```

从打印信息可以看出，该用 copy 修饰 NSString 属性后，对 NSMutableString 对象执行的修改就不会作用到 NSString 属性上，从上文对 copy 属性的讨论，我们知道此时是发生了深拷贝，生成了一个新的对象给当前属性，所以二者是相互独立的，基于对象的修改也不会影响到属性值。这样更加符合 NSString 的设计原则。

### 结论

使用 copy 操作修饰 NSString 属性，是为了避免在将 NSMutableString 对象赋值给属性时，因只进行浅拷贝而导致不可变的 NSString 属性会因为 NSMutableString 对象的内容修改而被修改的问题。
