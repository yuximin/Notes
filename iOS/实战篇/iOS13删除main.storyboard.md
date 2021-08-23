# iOS13 删除 main.storyboard 方法

iOS13 及以后，iOS 新增了一个 SceneDelegate 类，SceneDelegate 将负责 AppDelegate 的某些功能。最重要的是，window 的概念已被 scene 的概念所替代。一个应用程序可能具有不止一个场景，而一个场景现在可以作为应用程序的用户界面和内容的载体。

在 Xcode中新建 app 项目，会默认生成一个 main.storyboard 作为初始场景。很多时候，我们不想使用故事版布局，则需要做一些改动。

## 基于 Appdelegate

1. 删除Main.storyboard文件（SceneDelegate.h和SceneDelegate.m文件可删可不删）
2. 删除Info.plist文件Main storyboard file base name项和Application Scene Manifest项
3. 删除AppDelegate.m中如下代码

```objc
#pragma mark - UISceneSession lifecycle

- (UISceneConfiguration *)application:(UIApplication *)application configurationForConnectingSceneSession:(UISceneSession *)connectingSceneSession options:(UISceneConnectionOptions *)options {
    // Called when a new scene session is being created.
    // Use this method to select a configuration to create the new scene with.
    return [[UISceneConfiguration alloc] initWithName:@"Default Configuration" sessionRole:connectingSceneSession.role];
}


- (void)application:(UIApplication *)application didDiscardSceneSessions:(NSSet<UISceneSession *> *)sceneSessions {
    // Called when the user discards a scene session.
    // If any sessions were discarded while the application was not running, this will be called shortly after application:didFinishLaunchingWithOptions.
    // Use this method to release any resources that were specific to the discarded scenes, as they will not return.
}
```

4. 在 AppDelegate.h 和 AppDelegate.m 中添加如下代码

```objc
//
//  AppDelegate.h
//

#import <UIKit/UIKit.h>

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (nonatomic, strong) UIWindow *window;

@end
```

```objc
//
//  AppDelegate.m
//

#import "AppDelegate.h"
#import "ViewController.h"

@interface AppDelegate ()

@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    
    ViewController *vc = [[ViewController alloc] init];
    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
    self.window.rootViewController = vc;
    [self.window makeKeyAndVisible];
    
    return YES;
}

@end

```

## 基于 SceneDelegate

1. 删除Main.storyboard文件
2. 删除Info.plist文件 Main storyboard file base name 项和 Application Scene Manifest - Scene Configuration - Application Session Role - Item - Storyboard Name 项
3. 在 SceneDelegate.m 添加如下代码

```objc
- (void)scene:(UIScene *)scene willConnectToSession:(UISceneSession *)session options:(UISceneConnectionOptions *)connectionOptions {
    // Use this method to optionally configure and attach the UIWindow `window` to the provided UIWindowScene `scene`.
    // If using a storyboard, the `window` property will automatically be initialized and attached to the scene.
    // This delegate does not imply the connecting scene or session are new (see `application:configurationForConnectingSceneSession` instead).
    ViewController *vc = [[ViewController alloc] init];
    UIWindowScene *windowScene = (UIWindowScene *)scene;
    self.window = [[UIWindow alloc] initWithWindowScene:windowScene];
    self.window.frame = windowScene.coordinateSpace.bounds;
    self.window.rootViewController = vc;
    [self.window makeKeyAndVisible];
}
```
