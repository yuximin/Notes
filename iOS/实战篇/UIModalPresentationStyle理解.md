# UIModalPresentationStyle理解

`UIModalPresentationStyle` 是一个枚举，用于指定视图控制器的呈现样式。它定义了视图控制器如何在屏幕上呈现，包括全屏、弹出、底部卡片等不同的样式。这个枚举有以下几种常见的样式：

```swift
public enum UIModalPresentationStyle : Int, @unchecked Sendable {

    
    case fullScreen = 0

    @available(iOS 3.2, *)
    case pageSheet = 1

    @available(iOS 3.2, *)
    case formSheet = 2

    @available(iOS 3.2, *)
    case currentContext = 3

    @available(iOS 7.0, *)
    case custom = 4

    @available(iOS 8.0, *)
    case overFullScreen = 5

    @available(iOS 8.0, *)
    case overCurrentContext = 6

    @available(iOS 8.0, *)
    case popover = 7

    
    @available(iOS 7.0, *)
    case none = -1

    @available(iOS 13.0, *)
    case automatic = -2
}
```

## fullScreen

> 全屏显示视图控制器，遮盖整个屏幕

* 全屏显示
* 会触发上一个视图的 `Appear` 生命周期
* 不可通过下滑关闭

## pageSheet

> 以页表的形式显示视图控制器，与屏幕底部对齐，显示为半透明的模态视图，上方还留有一部分屏幕可见

* 非全屏显示
* 不会触发上一个视图的 `Appear` 生命周期
* 可通过下滑关闭

## formSheet

> 以表单的形式显示视图控制器，位于屏幕中央，显示为半透明的模态视图，周围有一些空白区域
>
> 实际测试效果与 `pageSheet` 一致，未发现什么区别

* 非全屏显示
* 不会触发上一个视图的 `Appear` 生命周期
* 可通过下滑关闭

## currentContext

> 在当前上下文中呈现视图控制器，通常用于以非模态方式显示一个视图控制器，但仍然显示在当前视图的上下文中。
>
> 当 `UITabBarController` 嵌套 `UINavigationController`，或 `UINavigationController` 嵌套 `UITabBarController` 中时，表现比较明显，视图控制器会被添加到最外层视图的 `TabBar` 或 `NavigationBar` 下层。

* 全屏显示
* 会触发上一个视图的 `Appear` 生命周期
* 不可通过下滑关闭

## custom

> 自定义样式，效果取决于不同的实现方式。默认效果类似于 `fullScreen`。

* 不会触发上一个视图的 `Appear` 生命周期

## overFullScreen

> 全屏显示视图控制器，遮盖整个屏幕。显示效果跟 `fullScreen` 相同，区别在于 `overFullScreen` 不会影响下层视图控制器的显示生命周期。

* 全屏显示
* 不会触发上一个视图的 `Appear` 生命周期
* 不可通过下滑关闭

## overCurrentContext

> 显示效果跟 `currentContext` 相同，区别在于 `overCurrentContext` 不会影响下层视图控制器的显示生命周期

* 全屏显示
* 不会触发上一个视图的 `Appear` 生命周期
* 不可通过下滑关闭

## popover

> 弹出视图，显示在特定的区域，如 iPad 上的弹出窗口。

* 非全屏显示
* 不会触发上一个视图的 `Appear` 生命周期
* 可通过下滑关闭
