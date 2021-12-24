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

