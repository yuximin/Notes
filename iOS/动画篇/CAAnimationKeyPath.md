# CAAnimation KeyPath

| keyPath                 | 值类型       | 说明                                 |
|:----------------------- | --------- |:---------------------------------- |
| transform.scale         | `CGFloat` | 整体缩放                               |
| transform.scale.x       | `CGFloat` | 仅宽度缩放                              |
| transform.scale.y       | `CGFloat` | 仅高度缩放                              |
| transform.rotation      | `CGFloat` | 平面旋转（同 `transform.rotation.z` ）    |
| transform.rotation.x    | `CGFloat` | 绕 x 轴（水平方向）旋转                      |
| transform.rotation.y    | `CGFloat` | 绕 y 轴（竖直方向）旋转                      |
| transform.rotation.z    | `CGFloat` | 绕 z 轴（垂直屏幕方向）旋转                    |
| transform.translation   | `CGPoint` | 平移                                 |
| transform.translation.x | `CGFloat` | 沿水平方向平移                            |
| transform.translation.y | `CGFloat` | 沿竖直方向平移                            |
| position                | `CGPoint` | 中心点位置                              |
| position.x              | `CGFloat` | 中心点的 x 轴位置                         |
| position.y              | `CGFloat` | 中心点的 y 轴位置                         |
| bounds                  | `CGRect`  | 位置和大小                              |
| bounds.origin.x         | `CGFloat` | 起始点 x 坐标                           |
| bounds.origin.y         | `CGFloat` | 起始点 y 坐标                           |
| bounds.size.width       | `CGFloat` | 宽度                                 |
| bounds.size.height      | `CGFloat` | 高度                                 |
| opacity                 | `CGFloat` | 不透明度                               |
| cornerRadius            | `CGFloat` | 圆角                                 |
| borderWidth             | `CGFloat` | 边框宽度                               |
| backgroundColor         | `CGColor` | 背景颜色                               |
| shadowColor             | `CGColor` | 阴影颜色                               |
| shadowOffset            | `CGPoint` | 阴影偏移量                              |
| shadowOpacity           | `CGFloat` | 阴影透明度                              |
| shadowRadius            | `CGFloat` | 阴影圆角                               |
| zPosition               | `CGFloat` | z轴位置                               |
| contents                | `CGImage` | 内容（图片）                             |
| contentsRect            | `CGRect`  | 内容可视区域（默认值为 (0.0, 0.0, 1.0, 1.0) ） |

## 注意事项

* 对 `bounds.origin.x` 和 `bounds.origin.y` 添加动画时，不会影响当前视图的显示状态，会对当前视图的子视图坐标位置产生影响。

* 对 `bounds.size.width` 和 `bounds.size.height` 添加动画时，宽高值是基于中心点不动的基础上改变的。视觉效果类似于 `transform.scale` 动画。

* `contentsRect` 属性为 `CGRect` 类型，其中 x, y, width, height 取值范围均为 [0, 1]。该属性的作用是截取内容的部分区域去填充当前视图。如 `UIImageView.layer.contentsRect = CGRect(0.5, 0.5, 0.5, 0.5)` ，是将图片从一半宽高的位置开始，截取一半宽高的内容，并将截取到的内容填充当前 UIImageView 视图。即使用图片右下四分之一部分内容填充当前视图。
