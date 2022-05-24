# UINavigationController

> 父类：UIViewController
>
> 导航控制器，系统提供的一种多视图控制器管理类。以栈的形式对其中的视图控制器进行管理，通过入栈和出栈操作实现视图的切换。

## 1. UINavigationController 的组成

* NavigationBar

* NavigationView

* NavigationToolBar

## 2. UINavigationControllerDelegate

```swift
public protocol UINavigationControllerDelegate : NSObjectProtocol {

    // 当 navigationController 通过 pushViewController, popViewController, setViewController 等操作切换新的 topViewController 时触发。
    // viewController 为即将展示的视图控制器。
    @available(iOS 2.0, *)
    optional func navigationController(_ navigationController: UINavigationController, willShow viewController: UIViewController, animated: Bool)

    // 同上
    @available(iOS 2.0, *)
    optional func navigationController(_ navigationController: UINavigationController, didShow viewController: UIViewController, animated: Bool)

    // 跟设备方向有关，暂不了解具体用法
    @available(iOS 7.0, *)
    optional func navigationControllerSupportedInterfaceOrientations(_ navigationController: UINavigationController) -> UIInterfaceOrientationMask

    // 跟设备方向有关，暂不了解具体用法
    @available(iOS 7.0, *)
    optional func navigationControllerPreferredInterfaceOrientationForPresentation(_ navigationController: UINavigationController) -> UIInterfaceOrientation

    // 跟控制器切换动画有关，暂不了解具体用法
    @available(iOS 7.0, *)
    optional func navigationController(_ navigationController: UINavigationController, interactionControllerFor animationController: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning?

    // 跟控制器切换动画有关，暂不了解具体用法
    @available(iOS 7.0, *)
    optional func navigationController(_ navigationController: UINavigationController, animationControllerFor operation: UINavigationController.Operation, from fromVC: UIViewController, to toVC: UIViewController) -> UIViewControllerAnimatedTransitioning?
}
```

UINavigationController 在进行视图切换时，UINavigationControllerDelegate 中的 willShow 和 didShow 以及 viewController 生命周期函数调用顺序如下：

### pushViewController

* viewController -- init

* viewController -- viewDidLoad

* viewController -- viewWillAppear

* UINavigationControllerDelegate -- willShow

* viewController -- viewWillLayoutSubviews

* viewController -- viewDidLayoutSubviews

* viewController -- viewDidAppear

* UINavigationControllerDelegate -- didShow

### popViewController

* viewController -- viewWillDisappear

* UINavigationControllerDelegate -- willShow

* viewController -- viewDidDisappear

* UINavigationControllerDelegate -- didShow

* viewController -- deinit

## 2. hidesBottomBarWhenPushed

> UIViewController 的扩展属性
>
> 被设置为 true 时，当此视图被 push 进入带有底部栏（如 TabBar）的控制器时，底部栏将会隐藏。
>
> 默认值为 false

## 自定义 UINavigationController 处理 pushViewController 时，TabBar 隐藏问题处理

重写 UINavigationController 的以下两个方法

```swift
override func pushViewController(_ viewController: UIViewController, animated: Bool) {
    /// 处理 pushViewController 时底部栏隐藏问题
    /// 1. push 第一个 viewController 时，底部栏默认展示，不做处理
    /// 2. push 第二个 viewController 时，设置其 hidesBottomBarWhenPushed 为 true，再执行 pushViewController 操作会隐藏底部栏。
    /// 3. 因为第二个 viewController 设置了 hidesBottomBarWhenPushed 为 true。之后再 push 进来的 viewController 默认都会隐藏底部栏，所以不用再对其进行设置。
    /// 注意：这里要先设置 hidesBottomBarWhenPushed 值，再执行 push 操作。否则，会有问题
    if viewControllers.count == 1 {
        viewController.hidesBottomBarWhenPushed = true
    }
    super.pushViewController(viewController, animated: animated)
}

override func setViewControllers(_ viewControllers: [UIViewController], animated: Bool) {
    /// 处理 setViewControllers 时底部栏隐藏问题
    /// 1. 使用 setViewControllers 设置 NavigationController 的 viewControllers 前，先处理好 hidesBottomBarWhenPushed 的值。
    /// 2. 同 pushViewController，只需要对第二个 viewController 的 hidesBottomBarWhenPushed 值进行处理即可。
    /// 3. 同样需要注意，先设置 hidesBottomBarWhenPushed 值，再执行 setViewControllers 操作。
    if viewControllers.count >= 2 {
        viewControllers[1].hidesBottomBarWhenPushed = true
    }
    super.setViewControllers(viewControllers, animated: animated)
}
```