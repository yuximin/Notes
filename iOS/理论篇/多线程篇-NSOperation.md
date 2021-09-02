# NSOperation

NSOperation是基于 GCD 之上更高一层的封装，NSOperation 需要配合 NSOperationQueue 来实现多线程。

NSOperation 实现多线程的步骤如下：

1. 创建任务：将需要执行的操作封装到 NSOperation 对象中

2. 创建队列：创建 NSOperationQueue 

3. 将任务加入到队列中：将创建好的 NSOperation 对象添加到 NSOperationQueue 中

需要注意的是，NSOperation 是一个抽象类，实际使用时需要用到它的子类。有以下三种形式：

* 使用子类 NSInvocationOperation
* 使用子类 NSBlockOperation
* 自定义继承自 NSOperation 的子类，通过实现内部的相应方法来封装任务

## NSOperationQueue

NSOperationQueue 存在一个特殊的队列：主队列。主队列对应于主线程，主队列上的任务都是在主线程上执行的。主队列由系统自动创建，可以通过如下代码获取：

```objective-c
NSOperationQueue *mainQueue = [NSOperationQueue mainQueue];
```

NSOperationQueue 有一个参数可以控制队列的最大并发数：maxConcurrentOperationCount。

maxConcurrentOperationCount 默认为 -1，表示不限制最大并发数，可以理解为并行队列。

当 maxConcurrentOperationCount 设置为 1 时，表示串行队列；当 maxConcurrentOperationCount 大于 1 时，表示并行队列。

**主队列的 maxConcurrentOperationCount 值默认为1**

将 NSOperation 添加到 NSOperationQueue 中，将会自动执行。代码如下：

```objective-c
NSOperationQueue *operationQueue = [[NSOperationQueue alloc] init];

NSInvocationOperation *invocationOperation = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(invocationOperationBlock) object:nil];

NSBlockOperation *blockOperation = [NSBlockOperation blockOperationWithBlock:^{
  NSLog(@"NSBlockOperation -- %@", [NSThread currentThread]);
}];

[operationQueue addOperation:invocationOperation];
[operationQueue addOperation:blockOperation];
```

运行结果如下：

```
2021-09-02 15:36:50.341366+0800 IOSLearning[11559:442733] invocationOperationBlock -- <NSThread: 0x600000fa0100>{number = 6, name = (null)}
2021-09-02 15:36:50.341438+0800 IOSLearning[11559:442733] NSBlockOperation -- <NSThread: 0x600000fa0100>{number = 6, name = (null)}
```

