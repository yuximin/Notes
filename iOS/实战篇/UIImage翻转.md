# UIImage翻转

## 实现代码

```swift
extension UIImage {
    public func orientationImage(_ orientation: UIImage.Orientation) -> UIImage {
        guard let cgImage = self.cgImage else { return self }
        return UIImage(cgImage: cgImage, scale: scale, orientation: orientation)
    }
}
```

## UIImage.Orientation 属性说明

### 源码

```swift
public enum Orientation : Int, @unchecked Sendable {
    case up = 0
    case down = 1
    case left = 2
    case right = 3
    case upMirrored = 4
    case downMirrored = 5
    case leftMirrored = 6
    case rightMirrored = 7
}
```

### 说明

* up 默认方向
* down 旋转180度
* left 逆时针旋转90度
* right 顺时针旋转90度
* upMirrored 左右镜像翻转
* downMirrored 左右镜像翻转再旋转180度
* leftMirrored 左右镜像翻转再逆时针旋转90度
* rightMirrored 左右镜像翻转再顺时针旋转90度