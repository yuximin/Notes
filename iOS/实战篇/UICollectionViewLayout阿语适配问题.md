# UICollectionViewLayout阿语适配问题

UICollectionViewLayout 针对阿拉伯语展示时，是不会处理方向的。如果要实现阿语环境下，UI镜像展示，则需要自定义 `Layout` ，并将 `flipsHorizontallyInOppositeLayoutDirection` 始终设为 `true`.

代码如下：

```swift
class CustomLayout: UICollectionViewLayout {
    override var flipsHorizontallyInOppositeLayoutDirection: Bool {
        get {
            return true
        }
    }
}
```