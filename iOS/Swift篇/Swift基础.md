## 常量与变量

1. 使用 let 声明常量，使用 var 声明变量。

2. 常量和变量声明时，必须确定值类型。你可以显示的指定值类型，也可以在常量和变量声明的时候通过赋初始值的方式来确定其值类型。如果初始值没有提供足够的信息，则需要显示指定值类型。

```swift
// 变量
var myVariable = 42
myVariable = 50

// 常量
let myConstant = 42
myConstant = 50 // 这里会报错，常量只能被赋值一次


// 显示指定值类型
let myConstant: Int
myConstant = 42
```

3. 值永远不会被隐式转换为其他类型。

```swift
let label = "The width is "
let width = 94
let widthLabel = label + String(width) // 去除 String 方法会报错
```

4. 值转字符串的便捷方法 `"\()"`

```swift
let width = 94
let label = "The width is \(width)"
```

## 可选类型

声明值类型的时候，在其后添加 `?` 符号，表示这个值是可选的，即可以为空。

```swift
let optionalString: String? = "Hello"
print(optionalString == nil)
```

可选类型的值在使用的时候需要对其解包，可以使用 `if let` 、`guard let` 以及 `??` 来处理值缺失的情况。

```swift
// 1
var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)" // Hello, John Appleseed
}

greeting = "Hello, \(optionalName)" // Hello, Optional("John Appleseed")
greeting = "Hello, \(optionalName ?? "Bob")" // Hello, John Appleseed

// 2
var optionalName: String? // 默认值为 nil
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)" // 这里不走
}
// Hello!
greeting = "Hello, \(optionalName)" // Hello, nil
greeting = "Hello, \(optionalName ?? "Bob")" // Hello, John Appleseed

// 3
var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
guard let name = optionalName else {
    return // optionalName 为空时直接 return
}
greeting = "Hello, \(name)" // Hello, John Appleseed
```

## switch

1. 运行 `switch` 中匹配到 `case` 语句之后，程序会退出 `switch` 语句，并不会继续向下执行。所以，不需要在每个 `case` 中添加 `break` 

2. `switch` 支持任意类型的数据以及各种比较操作。下面代码中的 `let x` 将匹配值赋值给 `x` 

```swift
let vegetable = "red pepper"
switch vegetable {
case "celery":
    print("Add some raisins and make ants on a log.")
case "cucumber", "watercress":
    print("That would make a good tea sandwich.")
case let x where x.hasSuffix("pepper"):
    print("Is it a spicy \(x)?")
default:
    print("Everything tastes good in soup.")
}
```

## 闭包

> 闭包，可以理解为代码块或是匿名函数。

```swift
// 一个标准的闭包格式
{ (参数...) -> 返回值 in

}
```

### 创建闭包的简短方式

1. 如果一个闭包的类型已知，比如作为一个代理的回调，你可以忽略参数，返回值，甚至两个都忽略。单个语句闭包会把它语句的值当做结果返回

```swift
let mappedNumbers = numbers.map({ number in 3 * number })
print(mappedNumbers)
```

2. 可以通过参数位置而不是参数名字来引用参数——这个方法在非常短的闭包中非常有用

3. 当一个闭包作为最后一个参数传给一个函数的时候，它可以直接跟在圆括号后面

4. 当一个闭包是传给函数的唯一参数，你可以完全忽略圆括号

```swift
let sortedNumbers = numbers.sorted { $0 > $1 }
print(sortedNumbers)
```

## @autoclosure

@autoclosure，自动闭包，用于将作为函数参数的表达式自动封装成闭包。

### 特性

* 可以省略闭包的大括号
* 不接受任何参数

### 示例代码

```swift
// 常规写法
func or(_ a: Bool, _ b: () -> Bool) {
  if a {
    return true
  }

  return b()
}

// 常规写法调用
or(2 > 3, {
  return 4 < 5
})

// 自动闭包写法
func or(_ a: Bool, _ b: @autoclosure () -> Bool) {
  if a {
    return true
  }

  return b()
}

// 自动闭包写法调用
// 函数的闭包参数添加了 @autoclosure 属性后，会自动将传入的表达式封装成闭包
or(2 > 3, 4 < 5)
```

## 枚举

1. 已知变量为枚举类型时，可以使用缩写的方式来引用枚举值

2. 枚举可以添加方法

```swift
enum Suit {
    case spades, hearts, diamonds, clubs
    func simpleDescription() -> String {
        switch self {
        case .spades:
            return "spades"
        case .hearts:
            return "hearts"
        case .diamonds:
            return "diamonds"
        case .clubs:
            return "clubs"
        }
    }
}
let hearts = Suit.hearts
hearts = .hearts // 缩写方式引用枚举值
let heartsDescription = hearts.simpleDescription() // hearts
```

3. 枚举的 `case` 可以添加参数

```swift
enum ServerResponse {
    case result(String, String)
    case failure(String)
}

let success = ServerResponse.result("6:00 am", "8:09 pm")
let failure = ServerResponse.failure("Out of cheese.")

switch success {
case let .result(sunrise, sunset):
    print("Sunrise is at \(sunrise) and sunset is at \(sunset)")
case let .failure(message):
    print("Failure...  \(message)")
}
```


