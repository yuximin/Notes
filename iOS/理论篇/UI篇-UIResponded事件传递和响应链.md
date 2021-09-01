# UIResponder事件传递和响应链

iOS 中常见事件可以分为以下类型：

* 触摸事件
* 按压事件
* 加速计事件
* 远程控制事件

## 概念

在探究事件传递和响应链之前，先了解一些基本概念。

### UIResponder

UIResponder 是 iOS 中用于处理用户事件的API，可以处理触摸事件、按压事件（3D touch）、加速计事件、远程控制事件。应用程序通过响应者来接收和处理事件，响应者可以是继承自 UIResponder 的任何子类。换句话说，**只有继承自 UIResponder 的对象，才能接收和处理事件**。iOS 中继承自 UIResopnder 的类型有：

* UIApplication
* UIViewController
* UIView

UIResponder 内部提供以下方法来对不同的事件进行处理。我们可以通过创建自定义类型，继承自 UIResponder 或它的子类，在新建的类中重写这些方法去监听对应事件，以此来实现一些自定义操作。

```objc
/// UIResponder.h

// 触摸事件
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(nullable UIEvent *)event;
- (void)touchesMoved:(NSSet<UITouch *> *)touches withEvent:(nullable UIEvent *)event;
- (void)touchesEnded:(NSSet<UITouch *> *)touches withEvent:(nullable UIEvent *)event;
- (void)touchesCancelled:(NSSet<UITouch *> *)touches withEvent:(nullable UIEvent *)event;
- (void)touchesEstimatedPropertiesUpdated:(NSSet<UITouch *> *)touches API_AVAILABLE(ios(9.1));

// 按压事件
- (void)pressesBegan:(NSSet<UIPress *> *)presses withEvent:(nullable UIPressesEvent *)event API_AVAILABLE(ios(9.0));
- (void)pressesChanged:(NSSet<UIPress *> *)presses withEvent:(nullable UIPressesEvent *)event API_AVAILABLE(ios(9.0));
- (void)pressesEnded:(NSSet<UIPress *> *)presses withEvent:(nullable UIPressesEvent *)event API_AVAILABLE(ios(9.0));
- (void)pressesCancelled:(NSSet<UIPress *> *)presses withEvent:(nullable UIPressesEvent *)event API_AVAILABLE(ios(9.0));

// 加速计事件
- (void)motionBegan:(UIEventSubtype)motion withEvent:(nullable UIEvent *)event API_AVAILABLE(ios(3.0));
- (void)motionEnded:(UIEventSubtype)motion withEvent:(nullable UIEvent *)event API_AVAILABLE(ios(3.0));
- (void)motionCancelled:(UIEventSubtype)motion withEvent:(nullable UIEvent *)event API_AVAILABLE(ios(3.0));

//远程控制事件
- (void)remoteControlReceivedWithEvent:(nullable UIEvent *)event API_AVAILABLE(ios(4.0));
```

### UIEvent

UIEvent 称为事件对象，用来记录事件产生的 **时刻** 和 **类型** 。每产生一个事件，就会对应生成一个 UIEvent 对象。在 UIEvent.h 文件中，包含定义了 iOS 中所有的事件类型的 UIEventType，以及定义事件子类型的 UIEventSubtype。具体代码如下：

```objc
/// UIEvent.h

// 事件类型
typedef NS_ENUM(NSInteger, UIEventType) {
    UIEventTypeTouches, //触摸类型
    UIEventTypeMotion, //加速计类型
    UIEventTypeRemoteControl, //远程控制类型
    UIEventTypePresses API_AVAILABLE(ios(9.0)), //按压类型
    UIEventTypeScroll      API_AVAILABLE(ios(13.4), tvos(13.4)) API_UNAVAILABLE(watchos) = 10,
    UIEventTypeHover       API_AVAILABLE(ios(13.4), tvos(13.4)) API_UNAVAILABLE(watchos) = 11,
    UIEventTypeTransform   API_AVAILABLE(ios(13.4), tvos(13.4)) API_UNAVAILABLE(watchos) = 14,
};

// 事件子类型
typedef NS_ENUM(NSInteger, UIEventSubtype) {
    // available in iPhone OS 3.0
    UIEventSubtypeNone                              = 0, //空类型
    
    // for UIEventTypeMotion, available in iPhone OS 3.0
    UIEventSubtypeMotionShake                       = 1, //摇动
    
    // for UIEventTypeRemoteControl, available in iOS 4.0
    UIEventSubtypeRemoteControlPlay                 = 100, //播放
    UIEventSubtypeRemoteControlPause                = 101, //暂停
    UIEventSubtypeRemoteControlStop                 = 102, //停止
    UIEventSubtypeRemoteControlTogglePlayPause      = 103,
    UIEventSubtypeRemoteControlNextTrack            = 104,
    UIEventSubtypeRemoteControlPreviousTrack        = 105,
    UIEventSubtypeRemoteControlBeginSeekingBackward = 106,
    UIEventSubtypeRemoteControlEndSeekingBackward   = 107,
    UIEventSubtypeRemoteControlBeginSeekingForward  = 108,
    UIEventSubtypeRemoteControlEndSeekingForward    = 109,
};
```

### UITouch

UITouch 用来记录跟触摸相关的信息，包括触摸的位置、时间、阶段等。当用户用一根手指触摸屏幕时，会创建一个与当前触摸事件相关联的 UITouch 对象。每根手指对应一个单独的 UITouch 对象。

当手指移动时，系统会更新同一个 UITouch 对象，使之能够一直保存该手指的信息。

当手指离开屏幕时，系统会销毁相应的 UITouch 对象。

在 UITouch.h 中，包含定义了所有触摸阶段类型的 UITouchPhase，其中包括触摸开始、移动、结束、取消等。

```objc
/// UITouch.h

// 触摸阶段
typedef NS_ENUM(NSInteger, UITouchPhase) {
    UITouchPhaseBegan,             // whenever a finger touches the surface.
    UITouchPhaseMoved,             // whenever a finger moves on the surface.
    UITouchPhaseStationary,        // whenever a finger is touching the surface but hasn't moved since the previous event.
    UITouchPhaseEnded,             // whenever a finger leaves the surface.
    UITouchPhaseCancelled,         // whenever a touch doesn't end but we need to stop tracking (e.g. putting device to face)
    UITouchPhaseRegionEntered   API_AVAILABLE(ios(13.4), tvos(13.4)) API_UNAVAILABLE(watchos),  // whenever a touch is entering the region of a user interface
    UITouchPhaseRegionMoved     API_AVAILABLE(ios(13.4), tvos(13.4)) API_UNAVAILABLE(watchos),  // when a touch is inside the region of a user interface, but hasn’t yet made contact or left the region
    UITouchPhaseRegionExited    API_AVAILABLE(ios(13.4), tvos(13.4)) API_UNAVAILABLE(watchos),  // when a touch is exiting the region of a user interface
};
```

## 事件传递

当我们用手指触摸屏幕时，会触发下面一系列操作，用来生成触摸事件，并寻找触摸事件的第一响应者。

1. iPhone OS 识别触摸动作，将其封装到 UITouch 和 UIEvent 对象中，生成触摸事件

2. RunLoop 接收触摸事件，将其放入当前应用程序的事件队列中

3. UIApplication 接收到事件，将事件传递给 keyWindow

4. keyWindow 遍历 subView 的 `hitTest:withEvent:` 方法，寻找点击区域内合适的视图来处理事件

5. UIView 的子视图也会遍历 subView 的 `hitTest:withEvent:` 方法，直到找到第一响应者

### 查找第一响应者

在事件传递过程中，我们的目的就是找到事件的第一响应者。查找第一响应者的过程，主要是通过不断调用 UIView 的以下两个 API 实现的。

```objc
- (nullable UIView *)hitTest:(CGPoint)point withEvent:(nullable UIEvent *)event;

- (BOOL)pointInside:(CGPoint)point withEvent:(nullable UIEvent *)event;
```

从 keyWindow 开始，逐步遍历其子视图，调用子视图的 `hitTest:withEvent:` 方法，通过该方法查找位于点击区域中的视图并返回，对返回的子视图不断执行该操作。如果子视图不在点击区域或者没有子视图，则当前视图就是第一响应者。**对于存在多个子视图的情况，后添加进来的视图会先进行判断。** 因为后添加进来的视图位于上方，更易于命中。

对于有以下三种情况的视图，`hitTest:withEvent:` 方法会返回 nil，即当前视图下无第一响应对象。

* hidden = 0
* userInteractionEnabled = NO
* alpha < 0.01

在 `hitTest:withEvent:` 方法内部，会调用 `pointInside:withEvent:` 方法，判断点击位置是否在当前视图的 bounds 内部。如果位于当前视图内部，则返回 YES；如果超出bounds，则返回 NO，同时 `hitTest:withEvent:` 返回 nil 。

## 响应链

在事件传递过程中，已经形成了一条响应链。所谓响应链，就是在事件传递链的基础上，将事件交给合适的视图来响应。

1. 应用程序首先会调用第一响应者来处理事件
2. 如果第一响应者不能处理该事件，则会交给其 nextResponder 属性所指向的对象来处理。对于 UIView 的 nextResponder 属性，如果有管理此 view 的 UIViewController ，则为此 UIViewController 对象；否则为它的 superView。
3. 类似的，如果响应链中的后续响应者对象也不能处理该事件，则将事件继续传给其 nextResponder 来处理，直到找到一个可以处理该事件的对象
4. 最上层的容器视图将事件传递给 UIWindow
5. UIWindow 将事件传递给 UIApplication 对象
6. 如果应用程序找不到响应者对象来处理该事件，则将该事件丢弃

