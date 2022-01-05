# UIView Animation

```swift
// 示例
UIView.animate(withDuration: 5) {
    view.transform = CGAffineTransform(translationX: 100, y: 0)
} completion: { completed in
    view.transform = CGAffineTransform.identity
}
```

上述代码完成如下动画，在 5 秒时间内，将 view 从初始位置向右偏移 100。完成之后，将 view 置回原处。
