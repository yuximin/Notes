# Session

```swift

/// 提供一个单例，方便快速使用，可通过全局对象 `AF` 调用
public static let `default` = Session()

public let session: URLSession
public let delegate: SessionDelegate

/// 必须为串行队列
public let rootQueue: DispatchQueue


public let startRequestsImmediately: Bool
public let requestQueue: DispatchQueue
public let serializationQueue: DispatchQueue
public let interceptor: RequestInterceptor?
public let serverTrustManager: ServerTrustManager?
public let redirectHandler: RedirectHandler?
public let cachedResponseHandler: CachedResponseHandler?
public let eventMonitor: CompositeEventMonitor
public let defaultEventMonitors: [EventMonitor] = [AlamofireNotifications()]

var requestTaskMap = RequestTaskMap()
var activeRequests: Set<Request> = []
var waitingCompletions: [URLSessionTask: () -> Void] = [:]
```
