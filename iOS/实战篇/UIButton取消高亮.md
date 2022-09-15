# UIButton取消高亮

解决方案，自定义UIButton，重写isHighlighted属性，使其始终为 `false` .

```swift
class UnHighlightedButton: UIButton {
    override var isHighlighted: Bool {
        get { false }
        set {}
    }
}
```