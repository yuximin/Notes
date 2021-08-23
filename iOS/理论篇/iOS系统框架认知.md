# iOS系统架构认知

## 架构层级

iOS系统底层架构大致分为一下四个层级，从下往上依次是Core OS（核心系统）、Core Services（核心服务）、Media（媒体）、Core Touch（交互）。

* Core OS

> 偏向系统硬件层级的支持，以及证书管理和签名算法等安全相关工具

常用框架：CoreBluetooth，Security

* Core Services

> 提供核心业务方面的支持，例如数据库、网络连接、网页访问等，尤其是Foundation框架，应该算是整个 Objective-C 的基石。常用的基础类以及多线程等都包含在这个框架内，也是属于开发者需要精通的框架之一。

常用框架：CFNetwork、Foundation、CoreData、WebKit

* Media

> 提供多媒体等方面的工具支持，基本上图片、语音、视频、OpenGL渲染、游戏引擎等都提供了对应的解决方案。

常用框架：CoreGraphics、CoreImage、AVFoundation、QuartzCore

* Core Touch

> 以界面开发工具为主，提供布局、交互、动画、推送等功能的支持，可以算是我们开发过程中运用最多的系统框架之一。

常用框架：UIKit、MapKit、NotificationCenter

