# CAAnimation KeyPath

| keyPath                 | 说明              |
|:-----------------------:|:---------------:|
| transform.scale         | 整体缩放            |
| transform.scale.x       | 仅宽度缩放           |
| transform.scale.y       | 仅高度缩放           |
| transform.rotation      | 旋转              |
| transform.rotation.x    | 绕 x 轴（水平方向）旋转   |
| transform.rotation.y    | 绕 y 轴（竖直方向）旋转   |
| transform.rotation.z    | 绕 z 轴（垂直屏幕方向）旋转 |
| transform.translation   | 平移              |
| transform.translation.x | 沿水平方向平移         |
| transform.translation.y | 沿竖直方向平移         |
| position                | 中心点位置           |
| position.x              | 中心点的 x 轴位置      |
| position.y              | 中心点的 y 轴位置      |
| bounds                  | 位置和大小           |
| bounds.origin.x         | 起始点 x 坐标        |
| bounds.origin.y         | 起始点 y 坐标        |
| bounds.size.width       | 宽度              |
| bounds.size.height      | 高度              |
| opacity                 | 不透明度            |
| cornerRadius            | 圆角              |
| borderWidth             | 边框宽度            |
| backgroundColor         | 背景颜色            |
| shadowColor             | 阴影颜色            |
| shadowOffset            | 阴影偏移量           |
| shadowOpacity           | 阴影透明度           |
| shadowRadius            | 阴影圆角            |

## 注意事项

* 对 bounds.origin.x 和 bounds.origin.y 添加动画时，不会影响当前视图的显示状态，会对当前视图的子视图坐标位置产生影响。

* 对 bounds.size.width 和 bounds.size.height 添加动画时，宽高值是基于中心点不动的基础上改变的。视觉效果类似于 transform.scale 动画。
