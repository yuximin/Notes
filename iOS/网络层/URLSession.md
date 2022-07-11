# URLSession

> `URLSession` 是 iOS 原生 API，用于创建和管理 `URLSessionTask`。

提供一个全局单例 `shared`

提供两个构造方法

```swift
public /*not inherited*/ init(configuration: URLSessionConfiguration)
public /*not inherited*/ init(configuration: URLSessionConfiguration, delegate: URLSessionDelegate?, delegateQueue queue: OperationQueue?)
```

## URLSessionConfiguration

URLSession 自定义配置。包括缓存策略、超时时间等。

`URLSessionConfiguration` 的构建方法将在 `iOS 13.0` 之后废弃。`URLSessionConfiguration` 需要通过其提供的类方法进行创建。

`URLSessionConfiguration` 包括以下类型：

```swift
open class var `default`: URLSessionConfiguration { get }

open class var ephemeral: URLSessionConfiguration { get }

@available(iOS 8.0, *)
open class func background(withIdentifier identifier: String) -> URLSessionConfiguration
```

## URLSessionTask

`URLSessionTask` 对应于单个网络请求任务。

`URLSessionTask` 包含以下子类：

* URLSessionDataTask

* URLSessionUploadTask

* URLSessionDownloadTask

* URLSessionStreamTask

### URLSessionDataTask

`URLSession` 提供以下方法创建 `URLSessionDataTask` :

```swift
open func dataTask(with request: URLRequest) -> URLSessionDataTask
    
open func dataTask(with url: URL) -> URLSessionDataTask

open func dataTask(with request: URLRequest, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask

open func dataTask(with url: URL, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask
```

**注意**

> 当创建 `URLSessionDataTask` 的时候，若绑定了 `completionHandler` ，则 `URLSession` 中绑定的 `delegate` 不会触发回调。这条规则对于其他类型的 Task 同样适用。

#### 示例一：通过 completionHandler 获取 Response

```swift
func loadData(_ urlString: String) {
    guard let url = URL(string: urlString) else { return }
            
    let task = URLSession.shared.dataTask(with: url) { [weak self] data, response, error in
        guard let httpResponse = (response as? HTTPURLResponse), httpResponse.statusCode == 200 else {
            return
        }
        
        guard let data = data else {
            return
        }
        
        // handle data
    }
    task.resume()
}
```

### 示例二：通过 delegate 获取 Response

```swift
func loadData(_ urlString: String) {
    guard let url = URL(string: urlString) else { return }
        
    let configuration = URLSessionConfiguration.default
    let session = URLSession(configuration: configuration, delegate: self, delegateQueue: delegateQueue)
    let task = session.dataTask(with: url)
    task.resume()
    session.finishTasksAndInvalidate()
}

func urlSession(_ session: URLSession, dataTask: URLSessionDataTask, didReceive response: URLResponse, completionHandler: @escaping (URLSession.ResponseDisposition) -> Void) {
    guard let httpResponse = response as? HTTPURLResponse, httpResponse.statusCode == 200 else {
        completionHandler(.cancel)
        return
    }
    
    completionHandler(.allow)
}

func urlSession(_ session: URLSession, dataTask: URLSessionDataTask, didReceive data: Data) {
    // handle data
}
```

### URLSessionUploadTask

### URLSessionDownloadTask

### URLSessionStreamTask

> @available(iOS 9.0, *)
