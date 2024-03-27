# Swift 奇淫巧技

### 代码解包极简版

```swift
let nickName: String? = "Jack"
if let nickName {
  print("Hello, \(nickName)")
}
```

### switch 字符串条件筛选

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

### 闭包的几种写法

```swift
var aClosure = {
    (number: Int) -> Int in
    return number * 3
}

var bClosure: (Int) -> Int = { number in
    return number * 3
}

var cClosure: (Int) -> Int = { $0 * 3 }

aClosure(3) // 9
bClosure(3) // 9
cClosure(3) // 9
```