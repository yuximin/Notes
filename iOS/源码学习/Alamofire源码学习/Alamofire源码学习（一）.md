# Alamofire

核心：Session类，对 URLSession 的封装，管理所有的请求调度。

## Session

```swift
// 确保之后的代码在指定队列上执行
dispatchPrecondition(condition: .onQueue(.main))

// 示例代码
DispatchQueue.global().async {
  // Something...
  
  // 执行到这句时，判断当前是不是在主队列，不是的话，程序不往下执行
  dispatchPrecondition(condition: .onQueue(.main))
  
  // UI操作
}
```

## Request

属性

```swift
/// `UUID` providing a unique identifier for the `Request`, used in the `Hashable` and `Equatable` conformances.
public let id: UUID
/// The serial queue for all internal async actions.
public let underlyingQueue: DispatchQueue
/// The queue used for all serialization actions. By default it's a serial queue that targets `underlyingQueue`.
public let serializationQueue: DispatchQueue
/// `EventMonitor` used for event callbacks.
public let eventMonitor: EventMonitor?
/// The `Request`'s interceptor.
public let interceptor: RequestInterceptor?
/// The `Request`'s delegate.
public private(set) weak var delegate: RequestDelegate?
```



### DataRequest

