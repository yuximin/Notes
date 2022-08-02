# 解决iOS15隐私跟踪(UserTracking)不弹授权框

## 背景

iOS 14.5之后，需要通过用户授权才能获取到用户的idfa。为了配合三方统计SDK获取用户idfa，在App启动的时候，就调用隐私跟踪授权请求，发起用户授权弹框。

## 问题

iOS 15.0及以上系统，用户授权弹框拉起失败

## 原因

授权请求调用时机过早，导致无法弹出

## 解决方案

1. 延时1s执行授权请求（注意：延时回调需在主线程执行）

2. 将授权请求放到 `applicationDidBecomeActive` 中执行（推荐）

## 示例代码

```swift
func applicationDidBecomeActive(_ application: UIApplication) {
    // 请求跟踪权限
    if #available(iOS 14, *) {
        ATTrackingManager.requestTrackingAuthorization { status in
            switch status {
            case .notDetermined:
                print("申请tracking权限，用户为做选择或未弹窗")
            case .restricted:
                print("申请tracking权限，用户为做选择或未弹窗")
            case .denied:
                print("申请tracking权限，用户拒绝")
            case .authorized:
                let idfa = ASIdentifierManager.shared().advertisingIdentifier.uuidString
                print("用户允许广告跟踪：\(idfa)")
            @unknown default:
                print("申请tracking权限，用户为做选择或未弹窗")
            }
        }
    } else {
        if ASIdentifierManager.shared().isAdvertisingTrackingEnabled {
            let idfa = ASIdentifierManager.shared().advertisingIdentifier.uuidString
            print("用户允许广告跟踪：\(idfa)")
        } else {
            print("用户限制了广告跟踪")
        }
    }
}
```

