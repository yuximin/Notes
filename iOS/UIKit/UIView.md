# UIView

> 父类 -- `UIResponder` 
>
> UIView 是一个基础的视图容器，它是其他视图的父视图。

## 属性

### isUserInteractionEnabled

> `Bool` 类型

是否启用用户交互。

默认值为 `true`。当设置为 `false` 时，UIView 无法响应用户交互事件。该设置会影响到子视图，当父视图设置为 `false` 时，子视图无论为何值都无法响应用户交互事件。

### tag

> `Int` 类型

用于标识视图对象