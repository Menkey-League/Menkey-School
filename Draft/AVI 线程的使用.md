三种方式的优缺点介绍：

1）NSThread:

优点：NSThread 比其他两个轻量级   
缺点：需要自己管理线程的生命周期，线程同步。线程同步对数据的加锁会有一定的系统开销  

Cocoa NSOperation

优点：不需要关心线程管理，数据同步的事情，可以把精力放在自己需要执行的操作上。
Cocoa operation 相关的类是 NSOperation ，NSOperationQueue。
NSOperation是个抽象类，使用它必须用它的子类，可以实现它或者使用它定义好的两个子
类：NSInvocationOperation 和 NSBlockOperation。
创建NSOperation子类的对象，把对象添加到NSOperationQueue队列里执行。


GCD
Grand Central Dispatch (GCD)是Apple开发的一个多核编程的解决方法。在iOS4.0开始之后才能使用。GCD是一个替代诸如NSThread, NSOperationQueue, NSInvocationOperation等技术的很高效和强大的技术。

-----------

### NSThread

NSThread 有两种直接创建方式：

- (id)initWithTarget:(id)target selector:(SEL)selector object:(id)argument 
+ (void)detachNewThreadSelector:(SEL)aSelector toTarget:(id)aTarget withObject:(id)anArgument

第一个是实例方法，第二个是类方法

```
1、[NSThread detachNewThreadSelector:@selector(doSomething:) toTarget:self withObject:nil];   
  
2、NSThread* myThread = [[NSThread alloc] initWithTarget:self   
                                        selector:@selector(doSomething:)   
                                        object:nil];   
[myThread start];
```

第一种方式会直接创建线程并且开始运行线程  
第二种方式是先创建线程对象，然后再运行线程操作，在运行线程操作前可以设置线程的优先级等线程信息

参数的意义：

+ selector ：线程执行的方法，这个selector只能有一个参数，而且不能有返回值。
+ target  ：selector消息发送的对象
+ argument:传输给target的唯一参数，也可以是

不显式创建线程的方法：

用NSObject的类方法  
performSelectorInBackground:withObject: 创建一个线程：
```[Obj performSelectorInBackground:@selector(doSomething) withObject:nil]; ```

关于锁

两种锁，一种NSCondition ，一种是：NSLock

使用指令 @synchronized 来简化 NSLock的使用

- (void)doSomeThing:(id)anObj 
{ 
    @synchronized(anObj) 
    { 
        // Everything between the braces is protected by the @synchronized directive. 
    } 
}

其他的一些锁对象，比如：循环锁NSRecursiveLock，条件锁NSConditionLock，分布式锁NSDistributedLock等等

```
// 在应用程序主线程中做事情：
performSelectorOnMainThread:withObject:waitUntilDone:
performSelectorOnMainThread:withObject:waitUntilDone:modes:

// 在指定线程中做事情：
performSelector:onThread:withObject:waitUntilDone:
performSelector:onThread:withObject:waitUntilDone:modes:

// 在当前线程中做事情：
performSelector:withObject:afterDelay:
performSelector:withObject:afterDelay:inModes:

// 取消发送给当前线程的某个消息
cancelPreviousPerformRequestsWithTarget:
cancelPreviousPerformRequestsWithTarget:selector:object:
```


### Cocoa NSOperation的使用


```
	NSInvocationOperation *operation = [[NSInvocationOperation 
			alloc]initWithTarget:self
			selector:@selector(downloadImage:)
			object:kURL];   
    NSOperationQueue *queue = [[NSOperationQueue alloc]init];   
    [queue addOperation:operation];   
    
```

下载完成后用performSelectorOnMainThread执行主线程.

队列里可以加入很多个NSOperation, 可以把NSOperationQueue看作一个线程池，可往线程池中添加操作（NSOperation）到队列中.

### GCD 

详细确实需要看官网 [GCD官方文档](https://developer.apple.com/library/ios/documentation/Performance/Reference/GCD_libdispatch_Ref/index.html#)

GCD的工作原理是：让程序平行排队的特定任务，根据可用的处理资源，安排他们在任何可用的处理器核心上执行任务。
 
一个任务可以是一个函数(function)或者是一个block。 GCD的底层依然是用线程实现，不过这样可以让程序员不用关注实现的细节。
 
GCD中的FIFO队列称为dispatch queue，它可以保证先进来的任务先得到执行。

1、常用的方法dispatch_async

```
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{ 
	dispatch_async(dispatch_get_main_queue(), ^{   
		// update ui  
         });
}
```

2、dispatch_group_async的使用

```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);   
dispatch_group_t group = dispatch_group_create();   
dispatch_group_async(group, queue, ^{   
    [NSThread sleepForTimeInterval:1];   
    NSLog(@"group1");   
});   
dispatch_group_async(group, queue, ^{   
    [NSThread sleepForTimeInterval:2];   
    NSLog(@"group2");   
});   
dispatch_group_async(group, queue, ^{   
    [NSThread sleepForTimeInterval:3];   
    NSLog(@"group3");   
});   
dispatch_group_notify(group, dispatch_get_main_queue(), ^{   
    NSLog(@"updateUi");   
});   
dispatch_release(group); 
```

3、dispatch_barrier_async的使用

dispatch_barrier_async是在前面的任务执行结束后它才执行，而且它后面的任务等它执行完成之后才会执行
例子代码如下：

```
dispatch_queue_t queue = dispatch_queue_create("gcdtest.rongfzh.yc", DISPATCH_QUEUE_CONCURRENT);   
dispatch_async(queue, ^{   
    [NSThread sleepForTimeInterval:2];   
    NSLog(@"dispatch_async1");   
});   
dispatch_async(queue, ^{   
    [NSThread sleepForTimeInterval:4];   
    NSLog(@"dispatch_async2");   
});   
dispatch_barrier_async(queue, ^{   
    NSLog(@"dispatch_barrier_async");   
    [NSThread sleepForTimeInterval:4];   
   
});   
dispatch_async(queue, ^{   
    [NSThread sleepForTimeInterval:1];   
    NSLog(@"dispatch_async3");   
});
```

```
2012-09-25 16:20:33.967 gcdTest[45547:11203] dispatch_async1 
2012-09-25 16:20:35.967 gcdTest[45547:11303] dispatch_async2 
2012-09-25 16:20:35.967 gcdTest[45547:11303] dispatch_barrier_async 
2012-09-25 16:20:40.970 gcdTest[45547:11303] dispatch_async3 
```


4、dispatch_apply 

执行某个代码片段N次。
dispatch_apply(5, globalQ, ^(size_t index) { 
    // 执行5次 
}); 


### 参考
[http://alvinzhu.me/blog/2013/10/06/iosde-duo-xian-cheng-fen-lei-yu-ying-yong/](http://alvinzhu.me/blog/2013/10/06/iosde-duo-xian-cheng-fen-lei-yu-ying-yong/)