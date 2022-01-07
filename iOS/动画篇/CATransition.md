# CATransition

> CATransition，过渡动画，是 CAAnimation 的子类。可以用于自定义控制器之间的转场动画，实现页面间切换的动画效果，以及对一些非动画属性的变化添加过渡动画。

先看一个例子：

```swift
let transition = CATransition()
transition.type = .fade
// transition.type = CATransitionType(rawValue: "rippleEffect")
transition.duration = 0.25
label.layer.add(transition, forKey: "TransitionAnimation")
label.text = title
```

上述代码，实现了对 UILable 视图中文字内容的改变添加过渡动画。

## CATransitionType

| 动画类型                  | 动画效果   | 是否支持方向 | 值类型 |
| --------------------- | ------ | ------ | --- |
| fade                  | 渐变（默认） | 否      | 枚举  |
| moveIn                | 覆盖     | 是      | 枚举  |
| push                  | 推出     | 是      | 枚举  |
| reveal                | 揭开     | 是      | 枚举  |
| pageCurl              | 页面掀开   | 只支持左右  | 字符串 |
| pageUnCurl            | 页面盖下   | 只支持左右  | 字符串 |
| rippleEffect          | 水滴效果   | 否      | 字符串 |
| suckEffect            | 吸走     | 否      | 字符串 |
| cube                  | 立方体    | 是      | 字符串 |
| oglFlip               | 翻转     | 是      | 字符串 |
| cameraIrisHollowOpen  | 镜头打开   | 否      | 字符串 |
| cameraIrisHollowClose | 镜头关闭   | 否      | 字符串 |

对于值类型为枚举的动画效果，直接赋值即可。对于值类型为字符串的动画效果，需要使用 `CATransitionType(rawValue: "rippleEffect")` 来进行转换。

## CATransitionSubtype

动画子类型，对支持方向的动画效果，控制动画运行的方向。具体支持如下方向控制：

| 类型         | 描述   |
| ---------- | ---- |
| fromRight  | 从右向左 |
| fromLeft   | 从左向右 |
| fromTop    | 从上向下 |
| fromBottom | 从下向上 |




