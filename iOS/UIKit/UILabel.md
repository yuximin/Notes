# UILabel

> 父类：`UIView`
>
> 文本视图

## 属性

| 字段名 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| text | String? | nil | 文本内容 |
| font | UIFont! | nil (system font 17 plain) | 字体 |
| textColor |  UIColor! | UIColor.label | 文本颜色 |
| shadowColor | UIColor? | nil (没有阴影) | 阴影颜色 |
| shadowOffset | CGSize | CGSizeMake(0, -1) | 阴影偏移 |
| textAlignment | NSTextAlignment | NSTextAlignmentNatural | 文本对齐方式 |
| lineBreakMode | NSLineBreakMode | NSLineBreakByTruncatingTail | 文本超出时的截断方式 |
| attributedText | NSAttributedString? | nil | 属性文本（带有属性的文本，该设置会覆盖上述属性中的设置） |
| highlightedTextColor | UIColor? | nil | 高亮状态时文本颜色 |
| isHighlighted | Bool | false | 是否为高亮状态 |
| isUserInteractionEnabled | Bool | false | 是否启用用户交互 |
| isEnabled | Bool | true | 是否为激活状态（设置为 false 时，文本会变暗，表示未激活，此时设置的文本颜色失效） |
| numberOfLines | Int | 1 | 文本显示行数 |
| adjustsFontSizeToFitWidth | Bool | false | 是否缩小文本字体以适配视图宽度 |
| baselineAdjustment | UIBaselineAdjustment | .alignBaselines | 文本字体缩小时，文本基线位置 |
| minimumScaleFactor | CGFloat | 0.0 | 文本字体的最小缩放比例 |
| allowsDefaultTighteningForTruncation | | 

### baselineAdjustment

`adjustsFontSizeToFitWidth` 为 `true` 且 `numberOfLines` 为 `1` 时生效

设置文本内容收缩时，文本对齐方式

```swift
public enum UIBaselineAdjustment : Int {

    case alignBaselines = 0 // default. used when shrinking text to position based on the original baseline

    case alignCenters = 1

    case none = 2
}
```

* alignBaselines

> 默认值。文本最上端于label中线对齐

* alignCenters

> 文本中线于label中线对齐

* none

> 文本最低端与label中线对齐

### minimumScaleFactor

`adjustsFontSizeToFitWidth` 为 `true` 时生效

当文本内容长度超过 UILabel 宽度时，文本内容会进行收缩。该属性用于控制文本内容收缩的最小比例，当文本内容收缩值低于该设置值时，文本将不再收缩。

默认值为 `0`。值为 `0` 时，不会限制文本缩小比例。