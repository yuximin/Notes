# KVO的实现原理及自定义KVO

## KVO实现原理

首先，通过一段代码，查看一下 KVO 运行效果。

```objc
// 添加 KVO 监听
Person *p = [[Person alloc] init];
p.name = @"NAME";

NSLog(@"begin: p.class = %s, p.setName = %p", object_getClassName(p), [p methodForSelector:@selector(setName:)]);

[p addObserver:self forKeyPath:@"name" options:NSKeyValueObservingOptionNew context:nil];

NSLog(@"end: p.class = %s, p.setName = %p", object_getClassName(p), [p methodForSelector:@selector(setName:)]);

p.name = @"NEW_NAME";
```

```objc
// KVO 回调
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context
{
    if ([keyPath isEqualToString:@"name"]) {
        NSLog(@"name change -- %@", change);
    } else {
        [super observeValueForKeyPath:keyPath ofObject:object change:change context:context];
    }
}
```

打印结果如下：

```
2021-07-04 14:44:42.835837+0800 IOSLearning[4520:337648] begin: p.class = Person, p.setName = 0x102441b64
2021-07-04 14:44:49.286011+0800 IOSLearning[4520:337648] end: p.class = CustomKVONotifying_Person, p.setName = 0x10244165c
2021-07-04 14:45:36.903278+0800 IOSLearning[4538:338569] name change -- {
    kind = 1;
    new = "NEW_NAME";
}
```

由上述代码运行结果可知：

对象 p 在添加 KVO 监听前后，其 class 类型以及 `setName:` 方法地址都发生了改变。

阅读 KVO 源码可知（我是看网上帖子说的），KVO 底层是通过 Runtime 实现的。当对某个对象添加了 KVO 监听，系统会动态创建一个名为 `CustomKVONotifying_对象原始类名` 的类，通过在该类中重写 setter 方法，并在 setter 方法中调用注册的 KVO 监听事件，以此来实现 KVO 监听机制。

**具体的实现细节，请自行阅读KVO源码。**

### 为什么要通过新建一个类的方式来实现KVO？

KVO 机制是在对象的属性发生改变时，自动触发注册的监听函数。要想实现该功能，则需要对对象的 Setter 进行重写。如果使用创建分类的方式来实现，则分类中的 Setter 方法会覆盖原先的 Setter 方法。这样的话，我们自定义的 Setter 方法就会失效。

## 自定义 KVO 的实现

```objc
/// setter 方法
void setterMethod(id self, SEL _cmd, NSString *value) {
    // 调用父类 setter 方法
    struct objc_super superClass = {
        self,
        class_getSuperclass([self class])
    };
  	objc_msgSendSuper(&superClass, _cmd, value);
    
    // 通知观察者调用observeValueForKeyPath方法
    id observer = objc_getAssociatedObject(self, (__bridge const void *)@"objc");
    NSString *methodName = NSStringFromSelector(_cmd);
    NSString *key = getValueKey(methodName);
  	objc_msgSend(observer, @selector(observeValueForKeyPath:ofObject:change:context:), key, self, @{@"kind": @1, @"new": value}, nil);
}

// 获取 setter 方法对应的属性名称
NSString *getValueKey(NSString *setter) {
    NSString *key = [setter substringWithRange:NSMakeRange(3, setter.length - 4)];
    NSString *letter = [[key substringToIndex:1] lowercaseString];
    key = [key stringByReplacingCharactersInRange:NSMakeRange(0, 1) withString:letter];
    return key;
}

/// 自定义KVO
- (void)cus_addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath options:(NSKeyValueObservingOptions)options context:(nullable void *)context {
    // 获取类名
    NSString *oldName = NSStringFromClass([self class]);
    NSString *newName = [NSString stringWithFormat:@"CustomKVONotifying_%@", oldName];
    
    // 创建类
    Class customClass = objc_allocateClassPair([self class], newName.UTF8String, 0);
    objc_registerClassPair(customClass);
    
    // 修改 isa 指针
    object_setClass(self, customClass);
    
    // 添加 setter 方法
    NSString *methodName = [NSString stringWithFormat:@"set%@:", keyPath.capitalizedString];
    SEL sel = NSSelectorFromString(methodName);
    class_addMethod(customClass, sel, (IMP)setterMethod, "v@:@");
    
    // 关联观察者对象
    objc_setAssociatedObject(self, (__bridge const void *)@"objc", observer, OBJC_ASSOCIATION_ASSIGN);
}
```

具体实现步骤如下：

1. 使用自定义 cus_addObserver 方法替换系统的 addObserver 方法，添加 KVO 监听
2. 自定义 cus_addObserver 方法的实现
    + 自定义子类，继承自当前对象所属类
    + 修改当前对象的 isa 指针，使其指向新建的子类
    + 为子类添加 Setter 方法
    + 添加关联对象，关联到观察者对象
3. Setter 方法的实现
    + 调用父类的 Setter 方法
    + 通知观察者，调用观察者内部注册的监听事件

**注意：**

在 Xcode12 中，使用 `objc_msgSend` 方法会报错，报错信息提示参数个数不匹配。

网上看到很多解决方案，都说修改 `BuildSetting` - `Enable Strict Checking of objc_msgSend Calls` 值为 NO。笔者在 Xcode12.5 中尝试无效。