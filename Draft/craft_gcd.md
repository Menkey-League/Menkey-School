#GCD

补充说明：如果要让一个程序块向一个外部定义的变量写入数据，使用__block定义一个变量，那么任何在相同作用域内定义的程序块都能直接访问它。

```
NSDate *startTime = [NSDate date];
dispatch_async(dispatch_get_global_queue(0, 0),^{

	__block NSString *firstResult;
	__block NSString *secondResult;
	
	dispatch_group_t group = dispatch_group_create();
	dispatch_group_async(group, dispatch_get_global_queue(0,0),^{
		firstResult = [self calculateFirstResult:processedData];
		});
	dispatch_group_async(group, dispatch_get_global_queue(0,0),^{
		secondResult = [self calculateSecondResult:processedData];
		});
	dispatch_group_notify(group, dispatch_get_global_queue(0,0),^{
		NSString *resultSummary = [NSString stringWithFormat: @"First: [%@]										\nSecond:[%@]",firstResult, secondResult];
		
		dispatch_async(dispatch_get_main_queue(),^{
			resultTextView.text = resultsSummary;
		});
		NSDate *endTime = [NSDate date];
		NSLog(@"Completed in %f seconds",[endTime timeIntervalSinceDate:startTime];
	});
```

GCD 串行队列与并发队列
GCD 延时执行
GCD 线程组
GCD 定时器
GCD 信号量
GCD 综合使用示例
