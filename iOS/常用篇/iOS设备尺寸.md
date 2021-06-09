| 机型 | 开发尺寸 | 分辨率 | 比例 | 屏幕尺寸 | 倍图 | 状态栏H | 导航栏H | tabbarH | safeBottom |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| iPhone 4/4S | 320 x 480 pt | 960 x 640 px | 3:2 | 3.5英寸 | @2x | 20 | 44 | 49 | 0 |
| iPhone 5/5c/5s/SE | 320 x 568 pt | 1136 x 640  px | 16:9 | 4.0英寸 | @2x | 20 | 44 | 49 | 0 |
| iPhone 6/6s/7/8 | 375 x 667 pt | 1134 x 750  px | 16:9 | 4.7英寸 | @2x | 20 | 44 | 49 | 0 |
| iPhone 6+/6s+/7+/8+ | 414 x 736 pt | 1920 x 1080 px | 16:9 | 5.5英寸 | @3x | 20 | 44 | 49 | 0 |
| iPhone XR | 414 x 896 pt | 1792 x 828  px | 19.5:9 | 6.1英寸 | @2x | 48 | 44 | 83 | 34 |
| iPhone X/XS | 375 x 812 pt | 2436 x 1125 px | 19.5:9 | 5.8英寸 | @3x | 44 | 44 | 83 | 34 |
| iPhone XS Max | 414 x 896 pt | 2688 x 1242 px | 19.5:9 | 6.5英寸 | @3x | 44 | 44 | 83 | 34 |
| iPhone 11 | 414 x 896 pt | 1792 × 828  px | 19.5:9 | 6.1英寸 | @2x | 48 | 44 | 83 | 34 |
| iPhone 11 Pro | 375 x 812 pt | 2436 × 1125 px | 19.5:9 | 5.8英寸 | @3x | 44 | 44 | 83 | 34 |
| iPhone 11 Pro Max | 414 x 896 pt | 2688 × 1242 px | 19.5:9 | 6.5英寸 | @3x | 44 | 44 | 83 | 34 |
| iPhone 12 / 12 Pro | 390 x 844 pt | 2532 x 1170 px | 19.5:9 | 6.1英寸 | @3x | 47 | 44 | 83 | 34 |
| iPhone 12 Pro Max | 428 x 926 pt | 2778 x 1284 px | 19.5:9 | 6.7英寸 | @3x | 47 | 44 | 83 | 34 |
| iPhone 12 mini | 375 x 812 pt | 2340 x 1080 px | 19.5:9 | 5.4英寸 | @3x | 44 | 44 | 83 | 34 |
 
```objc
NSLog(@"开发尺寸%f",[UIScreen mainScreen].bounds.size.width);
NSLog(@"开发尺寸%f",[UIScreen mainScreen].bounds.size.height);
NSLog(@"比例%f",[UIScreen mainScreen].bounds.size.width/[UIScreen mainScreen].bounds.size.height);
NSLog(@"状态栏H%f",[UIApplication sharedApplication].statusBarFrame.size.height);
NSLog(@"导航栏H%f",self.navigationController.navigationBar.bounds.size.height);
NSLog(@"tabbarH%f",self.tabBarController.tabBar.bounds.size.height);
if (@available(iOS 11.0, *)) {
  NSLog(@"%@",NSStringFromUIEdgeInsets([UIApplication sharedApplication].keyWindow.safeAreaInsets));
} else {
  // Fallback on earlier versions
}
NSLog(@"分辨率%@",NSStringFromCGSize([[UIScreen mainScreen] currentMode].size));
```
