# 关于 Swift 中的值类型和引用类型

## 值类型

swift 中值类型有：

* 基础数据类型

* 结构体

* 枚举

值类型在赋值时，进行的是深拷贝。赋值后的数据修改，不会影响到原始数据，二者在内存上是相互独立的。

**swift 中，值类型数据在执行赋值操作时并不会对原始数据进行深拷贝，只有对赋值后的数据进行修改时，才会进行深拷贝。**

```swift
/// 打印变量地址
func printPointer<T>(ptr: UnsafePointer<T>) {
    print(ptr)
}

var array0 = ["1", "2", "3", "4"]
var array1 = array0
print("array0: \(array0)")
printPointer(ptr: &array0)
print("array1: \(array1)")
printPointer(ptr: &array1)

array1[0] = "5"
print("array0: \(array0)")
printPointer(ptr: &array0)
print("array1: \(array1)")
printPointer(ptr: &array1)

```

控制台打印结果如下：

```swift
array0: ["1", "2", "3", "4"]
0x0000600000b85ee0
array1: ["1", "2", "3", "4"]
0x0000600000b85ee0

// array1 的值发生修改，地址变动。并且不会对 array0 的值产生影响
array0: ["1", "2", "3", "4"]
0x0000600000b85ee0
array1: ["5", "2", "3", "4"]
0x0000600000b860c0
```

稍微改变一下上面的代码：

```swift
var array0 = ["1", "2", "3", "4"]
var array1 = array0
print("array0: \(array0)")
printPointer(ptr: &array0)
print("array1: \(array1)")
printPointer(ptr: &array1)

array0[0] = "5"
print("array0: \(array0)")
printPointer(ptr: &array0)
print("array1: \(array1)")
printPointer(ptr: &array1)
```

控制台打印结果如下：

```swift
array0: ["1", "2", "3", "4"]
0x00006000027b6720
array1: ["1", "2", "3", "4"]
0x00006000027b6720

// 注意：这里是 array0 的地址发生了改变
array0: ["5", "2", "3", "4"]
0x00006000027be240
array1: ["1", "2", "3", "4"]
0x00006000027b6720
```

## 引用类型

swift 中的引用类型有：

* class

* 闭包

引用类型在赋值时，发生的是浅拷贝。即只是生成一个指针指向原对象所在内存地址，不会对原对象的内容进行复制。两个对象共用一块内存区间，任意一个对象的修改都会对共有同一内存的其它对象的值产生影响。

```swift
class People: NSObject {
    var name: String

    init(name: String) {
        self.name = name
    }
}

var p0 = People(name: "Bob")
var p1 = p0
print("p0.name: \(p0.name)")
printPointer(ptr: &p0)
print("p1.name: \(p1.name)")
printPointer(ptr: &p1)

p1.name = "June"
print("p0.name: \(p0.name)")
printPointer(ptr: &p0)
print("p1.name: \(p1.name)")
printPointer(ptr: &p1)
```

控制台打印结果如下：

```swift
p0.name: Bob
0x000000016d2b7590
p1.name: Bob
0x000000016d2b7588

p0.name: June
0x000000016d2b7590
p1.name: June
0x000000016d2b7588
```
