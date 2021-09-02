# GCD

## 一、GCD 简介

Grand Central Dispatch(GCD)是iOS开发上的一个多线程解决方案。它是一个在线程池模式的基础上执行的并发任务。

* CGD 会自动利用更多的 CPU 内核，提高效率和资源利用率
* GCD 自动管理线程的生命周期

## 二、GCD 的两个核型概念：任务和队列

### 1. 任务

任务就要执行的操作，即需要执行的一段代码。在GCD中任务是放在block中的。执行任务有两种方式：同步执行和异步执行。

#### 同步执行

阻塞当前线程，等待任务执行完成才释放当前线程。

#### 异步执行

不阻塞当前线程，仅将任务放入指定队列中，即可继续当前线程。

### 2. 队列

#### 串行队列

队列中的任务按照添加顺序依次执行。一个串行队列只对应一个线程。

#### 并行队列

队列中的任务开启多个线程同时执行。

## 三、代码示例


```objc
/// GCD测试
- (void)gcdTest {
    NSLog(@"Start Test -- Thread: %@", [NSThread currentThread]);
    
    [self syncSerialQueue];
//    [self asyncSerialQueue];
//    [self syncConcurrentQueue];
//    [self asyncConcurrentQueue];
//    [[[NSThread alloc] initWithTarget:self selector:@selector(syncMainQueue) object:nil] start];
//    [self asyncMainQueue];
    
    NSLog(@"End Test -- Thread: %@", [NSThread currentThread]);
}

/// 同步执行 + 串行队列
- (void)syncSerialQueue {
    /*
     同步执行 阻塞当前线程（不开辟新线程）
     串行队列 任务串行执行
     
     Start Test -- Thread: <NSThread: 0x600002610080>{number = 1, name = main}
     1 -- Thread: <NSThread: 0x600002610080>{number = 1, name = main}
     1 -- Thread: <NSThread: 0x600002610080>{number = 1, name = main}
     2 -- Thread: <NSThread: 0x600002610080>{number = 1, name = main}
     2 -- Thread: <NSThread: 0x600002610080>{number = 1, name = main}
     End Test -- Thread: <NSThread: 0x600002610080>{number = 1, name = main}
     */
    dispatch_queue_t queue = dispatch_queue_create("com.whaley.testQueue", DISPATCH_QUEUE_SERIAL);
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"1 -- Thread: %@", [NSThread currentThread]);
        }
    });
    
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"2 -- Thread: %@", [NSThread currentThread]);
        }
    });
}

/// 异步执行 + 串行队列
- (void)asyncSerialQueue {
    /*
     异步执行 不阻塞当前队列（开辟新线程）
     串行队列 任务串行执行（仅开辟1个新线程）
     
     Start Test -- Thread: <NSThread: 0x6000000e0280>{number = 1, name = main}
     End Test -- Thread: <NSThread: 0x6000000e0280>{number = 1, name = main}
     1 -- Thread: <NSThread: 0x6000000a7f40>{number = 2, name = (null)}
     1 -- Thread: <NSThread: 0x6000000a7f40>{number = 2, name = (null)}
     2 -- Thread: <NSThread: 0x6000000a7f40>{number = 2, name = (null)}
     2 -- Thread: <NSThread: 0x6000000a7f40>{number = 2, name = (null)}
     */
    dispatch_queue_t queue = dispatch_queue_create("com.whaley.testQueue", DISPATCH_QUEUE_SERIAL);
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"1 -- Thread: %@", [NSThread currentThread]);
        }
    });
    
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"2 -- Thread: %@", [NSThread currentThread]);
        }
    });
}

/// 同步执行 + 并发队列
- (void)syncConcurrentQueue {
    /*
     同步执行 阻塞当前线程(不开辟新线程)
     并发队列 由于是同步执行，并发队列中单次只存在单个任务，任务依次执行
     
     Start Test -- Thread: <NSThread: 0x60000086c440>{number = 1, name = main}
     1 -- Thread: <NSThread: 0x60000086c440>{number = 1, name = main}
     1 -- Thread: <NSThread: 0x60000086c440>{number = 1, name = main}
     2 -- Thread: <NSThread: 0x60000086c440>{number = 1, name = main}
     2 -- Thread: <NSThread: 0x60000086c440>{number = 1, name = main}
     End Test -- Thread: <NSThread: 0x60000086c440>{number = 1, name = main}
     */
    dispatch_queue_t queue = dispatch_queue_create("com.whaley.testQueue", DISPATCH_QUEUE_CONCURRENT);
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"1 -- Thread: %@", [NSThread currentThread]);
        }
    });
    
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"2 -- Thread: %@", [NSThread currentThread]);
        }
    });
}

/// 异步执行 + 并发队列
- (void)asyncConcurrentQueue {
    /*
     异步执行 不阻塞当前线程（开辟新线程）
     并发队列 多个任务开启多个线程，并行执行
     
     Start Test -- Thread: <NSThread: 0x600003de4900>{number = 1, name = main}
     End Test -- Thread: <NSThread: 0x600003de4900>{number = 1, name = main}
     2 -- Thread: <NSThread: 0x600003dbb640>{number = 7, name = (null)}
     1 -- Thread: <NSThread: 0x600003daaf00>{number = 6, name = (null)}
     1 -- Thread: <NSThread: 0x600003daaf00>{number = 6, name = (null)}
     2 -- Thread: <NSThread: 0x600003dbb640>{number = 7, name = (null)}
     */
    dispatch_queue_t queue = dispatch_queue_create("com.whaley.testQueue", DISPATCH_QUEUE_CONCURRENT);
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"1 -- Thread: %@", [NSThread currentThread]);
        }
    });
    
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"2 -- Thread: %@", [NSThread currentThread]);
        }
    });
}

/// 同步执行 + 主队列
- (void)syncMainQueue {
    /*
     同步执行 阻塞当前线程
     主队列 任务放入主队列中，串行执行（不开启新线程）
     
     Start Test -- Thread: <NSThread: 0x6000028d4080>{number = 1, name = main}
     End Test -- Thread: <NSThread: 0x6000028d4080>{number = 1, name = main}
     syncMainQueue Begin -- Thread: <NSThread: 0x60000288bd00>{number = 6, name = (null)}
     1 -- Thread: <NSThread: 0x6000028d4080>{number = 1, name = main}
     1 -- Thread: <NSThread: 0x6000028d4080>{number = 1, name = main}
     2 -- Thread: <NSThread: 0x6000028d4080>{number = 1, name = main}
     2 -- Thread: <NSThread: 0x6000028d4080>{number = 1, name = main}
     syncMainQueue End -- Thread: <NSThread: 0x60000288bd00>{number = 6, name = (null)}
     
     注意：当在主线程中调用同步执行 + 主队列，会发生死锁。原因是同步执行需要等待任务完成才能释放线程，否则一直阻塞。当前任务添加到主队列中，即放入主线程执行，任务需等待主线程释放才可执行，而此时主线程阻塞，在等待任务完成才释放，所以照成死锁。
     */
    NSLog(@"syncMainQueue Begin -- Thread: %@", [NSThread currentThread]);
    dispatch_queue_t queue = dispatch_get_main_queue();
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"1 -- Thread: %@", [NSThread currentThread]);
        }
    });
    
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"2 -- Thread: %@", [NSThread currentThread]);
        }
    });
    NSLog(@"syncMainQueue End -- Thread: %@", [NSThread currentThread]);
}

/// 异步执行 + 主队列
- (void)asyncMainQueue {
    /*
     异步执行 不阻塞当前线程
     主队列 任务放入主线程中，串行执行（不开启新线程）
     
     Start Test -- Thread: <NSThread: 0x600000fa0740>{number = 1, name = main}
     End Test -- Thread: <NSThread: 0x600000fa0740>{number = 1, name = main}
     1 -- Thread: <NSThread: 0x600000fa0740>{number = 1, name = main}
     1 -- Thread: <NSThread: 0x600000fa0740>{number = 1, name = main}
     2 -- Thread: <NSThread: 0x600000fa0740>{number = 1, name = main}
     2 -- Thread: <NSThread: 0x600000fa0740>{number = 1, name = main}
     */
    dispatch_queue_t queue = dispatch_get_main_queue();
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"1 -- Thread: %@", [NSThread currentThread]);
        }
    });
    
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"2 -- Thread: %@", [NSThread currentThread]);
        }
    });
}
```

