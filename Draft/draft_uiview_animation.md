#iOS Animation详解

本篇只要讲解iOS中动画的使用.
Animtion主要分为两类：UIView动画和CoreAnimation动画。
UIView动画有UIView属性动画，UIViewBlock动画，UIViewTransition动画。
而CoreAnimation动画主要通过CAAnimation和CALayer，常用的有CABasicAnimation,CAKeyframeAnimation,CATransition,CAAnimationGroup.


## UIView动画

### UIView属性动画

```
CGRect viewRect = CGRectMake(10,10,200,200);
self.myView= [[UIView alloc] initWithFrame:viewRect];
self.myView.backgroundColor = [UIColor whiteColor];
[self.view addSubview:self.myView];

//1 准备动画
//参数1: 动画的作用, 任意字符串，用来区分多个动画, 参数二: 传递参数用 nil:OC中使用
[UIView beginAnimations:@"changeSizeAndColor" context:nil];
//在准备动画的时候可以设置动画的属性
[UIView setAnimationDuration:0.7];
[UIView setAnimationDelegate:self];
[UIView setAnimationWillStartSelector:@selector(startAnimation)];
[UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];//动画的曲线
[UIView setAnimationRepeatCount:2];
[UIView setAnimationRepeatAutoreverses:YES];//动画往返执行, 必须设置动画的重复次数

//2 修改view的属性, 可以同时修改多个属性 注意:不是所有的属性都可以修改的(只有frame, center, bounds, backgroundColor, alpha, transform 可以修改)
self.myView.frame = CGRectMake(110, 100, 100, 100);
self.myView.backgroundColor = [UIColor colorWithRed:arc4random() % 256 / 255.0 green:arc4random() % 256 / 255.0 blue:arc4random() % 256 / 255.0 alpha:0.5];

//3 提交并执行动画
[UIView commitAnimations];
```


### Block动画

Block动画的实质是对UIView动画的封装

第一种

```
    [UIView animateWithDuration:2 animations:^{
        self.myView.backgroundColor = [UIColor orangeColor];
    }];
```

第二种

```
    [UIView animateWithDuration:2 animations:^{
        self.myView.backgroundColor = [UIColor orangeColor];
    } completion:^(BOOL finished) {
        //finished判断动画是否完成
        if (finished) {
            NSLog(@"finished");
        }
    }];
```

第三种

```
    [UIView animateWithDuration:2 delay:1 options:UIViewAnimationOptionCurveEaseInOut animations:^{
        //        设置要修改的View属性
        self.myView.backgroundColor = [UIColor orangeColor];
    } completion:^(BOOL finished) {
        //finished判断动画是否完成
        if (finished) {
            NSLog(@"finished");
        }
    }];
```

下面是AnimationOptionCurve参数：

```
    UIViewAnimationOptionCurveEaseInOut            = 0 << 16, // default
    UIViewAnimationOptionCurveEaseIn               = 1 << 16,
    UIViewAnimationOptionCurveEaseOut              = 2 << 16,
    UIViewAnimationOptionCurveLinear               = 3 << 16,
    
```

### UIViewTransition动画

```
    [UIView transitionWithView:self.myView duration:2 options:UIViewAnimationOptionTransitionFlipFromLeft animations:^{
        self.myView.backgroundColor = [UIColor orangeColor];
    } completion:^(BOOL finished) {
        //finished判断动画是否完成
        if (finished) {
            NSLog(@"finished");
        }
    }];
```

下面是AnimationOptionTransition参数：

```
    UIViewAnimationOptionTransitionNone            = 0 << 20, // default
    UIViewAnimationOptionTransitionFlipFromLeft    = 1 << 20,
    UIViewAnimationOptionTransitionFlipFromRight   = 2 << 20,
    UIViewAnimationOptionTransitionCurlUp          = 3 << 20,
    UIViewAnimationOptionTransitionCurlDown        = 4 << 20,
    UIViewAnimationOptionTransitionCrossDissolve   = 5 << 20,
    UIViewAnimationOptionTransitionFlipFromTop     = 6 << 20,
    UIViewAnimationOptionTransitionFlipFromBottom  = 7 << 20,
```

-------------

## CoreAnimation动画

Core Animation是一组非常强大的动画处理API,使用它能做出非常绚丽的动画效果,而且往往是事半功倍,使用它需要添加QuartzCore .framework和引入对应的框架<QuartzCore/QuartzCore.h>

开发步骤:

1. 初始化一个动画对象(CAAnimation)并设置一些动画相关属性.

2. 添加动画对象到层(CALayer)中,开始执行动画.


CALayer中很多属性都可以通过CAAnimation实现动画效果, 包括opacity, position, transform, bounds, contents等(可以在API文档中搜索:CALayer Animatable Properties).

 通过调用CALayer的addAnimation:forKey:增加动画到层(CALayer)中,这样就能触发动画了.通过调用removeAnimationForKey:可以停止层中的动画.  
Core Animation的动画执行过程都是在后台操作的,不会阻塞主线程.

### CABasicAnimation基本动画

CABasicAnimation基本动画没有真正的修改属性值

初始化UIView的layer

```
	CGRect viewRect = CGRectMake(50,100,200,200);
    
    self.myView= [[UIView alloc] initWithFrame:viewRect];
    self.myView.backgroundColor = [UIColor whiteColor];
    
    self.myView.layer.cornerRadius = 100;//设置圆角, 参数是内切圆的半径, 若想画一个圆, 前提是view必须是正方形, 参数应该是view边长的一半
    self.myView.layer.borderWidth = 5;//设置描边的宽度
    self.myView.layer.borderColor = [UIColor orangeColor].CGColor;//设置描边的颜色(UIView上的颜色使用的是UIColor, CALayer上使用的颜色是CGColor)
    self.myView.layer.shadowOffset = CGSizeMake(50, 100);//设置阴影的偏移量 width影响水平偏移(正右负左), height影响垂直偏移(正下负上)
    self.myView.layer.shadowColor = [UIColor redColor].CGColor;//阴影的偏移的颜色
    self.myView.layer.shadowOpacity = 0.5;//阴影的不透明度, 取值范围(0 ~ 1), 默认是0, 就是透明的
    
    [self.view addSubview:self.myView];
```

动画方法：

```
    //1 创建并指定要修改的属性
    //    KeyPath:CAlayer的属性名, 不是所有的属性都可以, 只有在头文件中出现animatable的属性才可以, 可以修改属性的属性, 例如:bounds.size
    CABasicAnimation *basic = [CABasicAnimation animationWithKeyPath:@"bounds"];
    [basic setDuration:0.7];
    //2 修改属性值
    basic.fromValue = [NSValue valueWithCGRect:CGRectMake(0, 0, self.myView.bounds.size.width, self.myView.bounds.size.height)];
    basic.toValue = [NSValue valueWithCGRect:CGRectMake(0, 0, 300, 300)];
    //3 添加动画
    //key做区分动画用
    [self.myView.layer addAnimation:basic forKey:@"changColor"];
```

### CAKeyframeAnimation关键帧动画

示例1，和上面的动画一样

```
     //1 创建动画
     CAKeyframeAnimation *keyFrame = [CAKeyframeAnimation animationWithKeyPath:@"bounds"];
     [keyFrame setDuration:2];
     //2 修改属性
     keyFrame.values = @[[NSValue valueWithCGRect:CGRectMake(0, 0, self.myView.bounds.size.width, self.myView.bounds.size.height)], [NSValue valueWithCGRect:CGRectMake(0, 0, 250, 250)], [NSValue valueWithCGRect:CGRectMake(0, 0, 300, 300)]];
    //keyTimes:值代表了出现动画的时刻, 值得范围是0~1, 值必须是递增的, keyTimes和values是一一对应的
     keyFrame.keyTimes = @[@(0.4), @(0.6), @(1)];
     //3 添加动画
     [self.myView.layer addAnimation:keyFrame forKey:@"keyFrame"];  
```

示例2，改变颜色

```
    CAKeyframeAnimation *keyFrame = [CAKeyframeAnimation animationWithKeyPath:@"backgroundColor"];
    [keyFrame setDuration:3];
    keyFrame.values = @[(id)[UIColor redColor].CGColor, (id)[UIColor orangeColor].CGColor, (id)[UIColor yellowColor].CGColor, (id)[UIColor greenColor].CGColor, (id)[UIColor blueColor].CGColor];
    keyFrame.keyTimes = @[@(0.3), @(0.5), @(0.6), @(0.7), @(0.9)];
    [self.myView.layer addAnimation:keyFrame forKey:nil];
```

### CATransaction 过度动画

```
    //1 创建
    CATransition *transition = [CATransition animation];
    [transition setDuration:2];
    //2 设置过度样式
    transition.type = kCATransitionReveal;//控制样式
    transition.subtype = kCATransitionFromTop;//控制方向
    //添加动画
    [self.myView.layer addAnimation:transition forKey:nil];
```

### CAAnimationGroup 组动画

```
    //1 创建并指定要修改的属性
    //    KeyPath:CAlayer的属性名, 不是所有的属性都可以, 只有在头文件中出现animatable的属性才可以, 可以修改属性的属性, 例如:bounds.size
    //    CALayer
    CABasicAnimation *basic = [CABasicAnimation animationWithKeyPath:@"bounds"];
    [basic setDuration:2];
    //2 修改属性值
    basic.fromValue = [NSValue valueWithCGRect:CGRectMake(0, 0, self.myView.bounds.size.width, self.myView.bounds.size.height)];
    basic.toValue = [NSValue valueWithCGRect:CGRectMake(0, 0, 300, 300)];
    
    CAKeyframeAnimation *keyFrame = [CAKeyframeAnimation animationWithKeyPath:@"backgroundColor"];
    [keyFrame setDuration:2];
    keyFrame.values = @[(id)[UIColor redColor].CGColor, (id)[UIColor orangeColor].CGColor, (id)[UIColor yellowColor].CGColor, (id)[UIColor greenColor].CGColor, (id)[UIColor blueColor].CGColor];
    //keyTimes中的第一个值是0, 不能修改
    keyFrame.keyTimes = @[@(0.3), @(0.5), @(0.6), @(0.7), @(0.9)];

    // 创建
    //当group动画的时长 > 组中所有动画的最长时长, 动画的时长以组中最长的时长为准
    //当group动画的时长 < 组中所有动画的最长时长, 动画的时长以group的时长为准
    //最合适: group的时长 = 组中所有动画的最长时长
    CAAnimationGroup *group = [CAAnimationGroup animation];
    [group setDuration:10];
    //设置组动画
    group.animations = @[basic, keyFrame];
    //添加动画
    [self.myView.layer addAnimation:group forKey:nil];
```

-------------

## UIView知识点

### UIView官方文档

继承自:UIResponder:NSObject 

遵循:UITraitEnvironment, UIDynamicItem, NSObject, UICoordinateSpace, UIAppearanceContainer, UIAppearance, NSCoding
框架:iOS 2.0及以后版本的UIKit

因为View对象是应用与用户交互的主要途径，他们有一系列的职责。以下列出一些: 

+ 绘画和动画:
	+ View在他们的矩形区域使用UIKit,Core Graphics和OpenGL ES之类的技术绘画
	+ 一些视图属性可以动画到新的值
+ 布局和子View管理
	+ 一个View可以包含0个或多个子View
	+ 每个View都定义了相对父View大小改变的默认行为
	+ 如有需要，一个View可以定义子View的大小和位置
+ 事件处理
	+ View是一个响应者，可以处理UIResponder类定义的触摸和其他事件
	+ View可以使用addGestureRecognizer方法安装手势识别器来处理常用手势

View可以内嵌到其他View，创建复杂的可视层级。这会在嵌入的View(子View)和被嵌入的View(父View)间创建父子关系。正常情况下，子View的可视区域不会被父View的边界剪辑，但在iOS里你可以使用clipsToBounds属性来修改这个行为。一个父View可能有多个子View,但一个子View只有一个父View,它负责确定子View的位置。

View的几何结构是通过它的框架(frame),边界(bounds)和中心(center)属性来定义的。frame定义了View相对父View坐标系的原点和大小,一般在View布局和调整尺寸或位置时使用。center属性可以在不改变view的大小来调整view的位置。bounds定义了view的内部尺寸，几乎只在自定义绘画代码时使用。frame的size部分和bounds矩形部分耦合在一起，所以可以使用两者中的一个或两个同时来改变view的大小。

### 创建一个View

用代码来创建View: 

swift:

```
let viewRect = CGRect(x: 10, y: 10, width: 100, height: 100)
let myView = UIView(frame: viewRect)
```

Objective-c

```
CGRect  viewRect = CGRectMake(10, 10, 100, 100);
UIView* myView = [[UIView alloc] initWithFrame:viewRect];
```
这段代码创建了view,并把它放在父view坐标系的(10,10)点(一旦它被加到父view)。添加一个子view到另一个view上，用addSubview:方法。在iOS里，同级的view可以相互覆盖，而不会有任何问题，允许复杂的view布置。addSubview放置指定的view到它的同级view的顶部。你可以使用insertSubview:aboveSubview: 和 insertSubview:belowSubview: 方法指定子view的相对z坐标。你也可以使用exchangeSubviewAtIndex:withSubviewAtIndex: 方法交换已经添加的子view的位置。


### View绘画周期

View绘画发生在需要时。当一个view第一次展示时，或者在布局变化时，它的整体或部分变得可见时，系统请求view绘出它的内容。对那些使用UIKit或Core Graphics包含自定义内容的view来说，系统会调用它的drawRect: 方法。你对该方法的实现，负责将view的内容画进当前的图形上下文(graphics context),系统自动优先调用该方法。这里创建了view内容的静态可视展现，接着会被展示在屏幕上。

当view的真实内容发生改变时，你有责任通知系统你的view需要重绘。通过调用view的setNeedsDisplay或setNeedsDisplayInRect：方法来通知。这些方法让系统知道它应该在下次绘画周期更新View.因为它一直等待直到下次绘画周期来更新View,你可以在多个view上调用这些方法，同时更新它们.

### 动画

改变多个View属性可以被动画展现，在一个短周期时间内，改变属性可以创建动画。UIView类做了很多执行动画的工作，但你仍然必须判断你需要哪些属性改变被动画。下面是两种不同的初始化动画方法：

+ 在iOS4.0及以后的版本中，使用基于块的动画方法(推荐)
+ 使用开始/提交(begin/commit)动画方法。

基于块的动画方法(像animateWithDuration:animations:)极大地简化了动画创建。只要调用一个方法，指定动画的参数，并且执行动画。然后，基于块的动画仅在iOS4及以后版本中可用。如果你的设备运行老版本的iOS,必须使用beginAnimations:context: 和 commitAnimations类方法标记动画开始和结束。

下面是动画属性:

+ @property frame
+ @property bounds
+ @property center
+ @property transform
+ @property alpha
+ @property backgroundColor
+ @property contentStretch

线程注意事项

操作用户界面必须发生在主线程。因此，你必须在应用主线程调用UIView类的方法。只有在创建view对象自身时可以不用严格遵守，但其他操作必须在主线程中。

### 子类化说明

对同样需要用户交互的可见内容来说，UIView类是一个关键的子类化点。尽管有很多好理由继承UIView,但我们只推荐在基本的UIView和系统自带的其他组件不能满足需要时继承UIView.继承UIView会在你实现的代码里消耗更多性能.(Apple写的代码比你的更好，有现成的就用现成的)。

#### 覆盖方法

当子类化UIView时，只有少数的方法你必须覆盖，大多数方法你可以按需覆盖。因为UIView是一个高度可配的类，不用覆盖父类方法，同样有很多实现复杂的行为的途径，这些在 选择子类化 一节中介绍。你可以在你的UIView子类中考虑覆盖下面列表中的方法：

+ 初始化
	+ initWithFrame 推荐覆盖。你同样可以实现自定义的初始化方法添加或代替此方法
	+ initWithCoder: 如果你从一个Interface Builder的nib文件加载view，并且需要自定义初始化，覆盖该方法。
	+ layerClass 仅当你的view需要用不同的Core Animation层后备保存时才要覆盖。例如，当你的view需要用平铺方式显示一块很大的可滚动区域时，你可能想要覆盖该方法返回CATiledLayer类.(不是很明白，原文： Implement this method only if you want your view to use a different Core Animation layer for its backing store. For example, if your view uses tiling to display a large scrollable area, you might want to override this method and return the CATiledLayer class.)
+ 绘画和打印
	+ drawRect: 如果你的View画自定义的内容，就要实现该方法，否则避免覆盖该方法。
	+ drawRect:forViewPrintFormatter: 仅当你需要在打印时，打印不同内容(与显示不同)才需要实现该方法。
+ 约束
	+ requiresConstraintBasedLayout – 如果你的View类需要约束才能正常工作，实现该方法
	+ updateConstraints 如果你的view需要在子view间创建约束，需要实现该方法
	+ alignmentRectForFrame:, frameForAlignmentRect: – 实现这些方法覆盖你的view如何与其他view对齐
+ 布局
	+ sizeThatFits: – 当你想在执行resize操作时有一个不同于默认的size，实现该方法。比如，你可以用这个方法阻止view收缩到子view不能正确显示的点
	+ layoutSubviews – 如果你需要更精确控制子view，而不是使用限制或autoresizing行为，就需要实现该方法。
	+ didAddSubview: , willRemoveSubview: 跟踪子view添加或删除事件
	+ willMoveToSuperview:, didMoveToSuperview 跟踪当前view在view层次里的运动
	+ willMoveToWindow:,didMoveToWindow 跟踪view(即将或已经)移动到另一个Window
事件处理:
	+ touchesBegan:withEvent:, touchesMoved:withEvent:, touchesEnded:withEvent:, touchesCancelled:withEvent: 直接处理触摸事件(如果是手势，使用gesture recognizers)
	+ gestureRecognizerShouldBegin: 如果需要直接处理触摸事件，那么需要覆盖该方法，阻止手势识别器触发额外动作。

#### 替代子类化

很多view行为可以配置而不用子类化。在你开始覆盖这些方法时，考虑是否可以修改下面的属性或行为来提供你需要的功能。  

* addConstraint: – 为view和它的子view定义自动布局行为.
* autoresizingMask – 当父view的frame改变时，提供自动布局行为。这些行为可以和约束(Constraint)合并
* contentMode -为view内容提供布局行为，与view的frame相反。这个属性同样影响内容应用view的缩放，是缓存还是重绘。
* contentStretch-定义view的部分可伸缩。这个行为通常用于实现按钮和其他复杂布局、可变尺寸、重绘代价高的view。
* hidden 或 alpha 改变view的不透明度或隐藏view
* backgroundColor – 设置view的背景颜色
* Subviews 不在drawRect方法里绘制你的内容，使用嵌入图片或文本子view等方式
* Gesture recognizers – 使用手势识别器替代自己手工处理touch事件
* Animations – 使用内建动画支持代替自己写动画。Core Animation提供的动画支持很快很好用
* 基于图片的背景 – 对那些显示相对静态内容的view来说，考虑使用UIImageView对象加上手势识别替代子类化和自己绘制图片。同样，你也可以使用一般的UIView对象，分配你的图片作为view的CALayer对象内容。

动画是不需要子类化和实现复杂代码而让视觉改变的另一种方式。很多UIView的属性是可以动画的，意味着改变这些属性可以触发系统生成动画。启动动画只需要很少的一行代码指示那些改变需要被动画。更多view动画的信息，参考Animations.

-------------

## CoreAnimation相关知识

### CAAnimation

    所有动画对象的父类,负责控制动画的持续时间和速度,是个抽象类,不能直接使用,应该使用它具体的子类.

属性解析:

    duration：动画的持续时间 .

    repeatCount：动画的重复次数 .

    repeatDuration：动画的重复时间 .

    removedOnCompletion：默认为YES，代表动画执行完毕后就从图层上移除，图形会恢复到动画执行前的状态。如果想让图层保持显示动画执行后的状态，那就设置为NO，不过还要设置fillMode为kCAFillModeForwards .

    fillMode：决定当前对象在非active时间段的行为.比如动画开始之前,动画结束之后 .

    beginTime：可以用来设置动画延迟执行时间，若想延迟2s，就设置为CACurrentMediaTime()+2，                            CACurrentMediaTime()为图层的当前时间 .

    timingFunction：速度控制函数，控制动画运行的节奏 .

    delegate：动画代理  

 速度控制函数(CAMediaTimingFunction)

    1. kCAMediaTimingFunctionLinear（线性）：匀速，给你一个相对静态的感觉

    2. kCAMediaTimingFunctionEaseIn（渐进）：动画缓慢进入，然后加速离开

    3. kCAMediaTimingFunctionEaseOut（渐出）：动画全速进入，然后减速的到达目的地

    4. kCAMediaTimingFunctionEaseInEaseOut（渐进渐出）：动画缓慢的进入，中间加速，然后减速的到达目的地。        这个是默认的动画行为。

CAAnimation在分类中定义了代理方法

    @interface NSObject <CAAnimationDelegate>

    - (void)animationDidStart:(CAAnimation *)anim;

    - (void)animationDidStop:(CAAnimation *)anim finished:(BOOL)flag;

    @end

fillMode属性值（要想fillMode有效，最好设置removedOnCompletion=NO）

    kCAFillModeRemoved 这个是默认值,也就是说当动画开始前和动画结束后,动画对layer都没有影响,动画结束后,layer会恢复到之前的状态 .

    kCAFillModeForwards 当动画结束后,layer会一直保持着动画最后的状态.

     kCAFillModeBackwards 在动画开始前,你只要将动画加入了一个layer,layer便立即进入动画的初始状态并等待动画开始. 你可以这样设定测试代码,将一个动画加入一个layer的时候延迟5秒执行.然后就会发现在动画没有开始的时候,只要动画被加入了layer,layer便处于动画初始状态  

    kCAFillModeBoth 这个其实就是上面两个的合成.动画加入后开始之前,layer便处于动画初始状态,动画结束后layer保持动画最后的状

### CAPropertyAnimation

CAAnimation的子类，也是个抽象类，要想创建动画对象，应该使用它的两个子类：CABasicAnimation和CAKeyframeAnimation.

属性解析：

keyPath：

    通过指定CALayer的一个属性名称为keyPath(NSString类型)，并且对CALayer的这个属性的值进行修改，达到相应的动画效果。比如，指定@”position”为keyPath，就修改CALayer的position属性的值，以达到平移的动画效果

CABasicAnimation    

    CAPropertyAnimation的子类 .

属性解析: 

    fromValue：keyPath相应属性的初始值 .

    toValue：keyPath相应属性的结束值 .

    随着动画的进行，在长度为duration的持续时间内，keyPath相应属性的值从fromValue渐渐地变为toValue .


如果fillMode=kCAFillModeForwards和removedOnComletion=NO，那么在动画执行完毕后，图层会保持显示动画执行后的状态。但在实质上，图层的属性值还是动画执行前的初始值，并没有真正被改变。比如，CALayer的position初始值为(0,0)，CABasicAnimation的fromValue为(10,10)，toValue为(100,100)，虽然动画执行完毕后图层保持在(100,100)这个位置，实质上图层的position还是为(0,0)
    
```
//平移动画
CABasicAnimation *anim = [CABasicAnimation animationWithKeyPath:@"position"];
// 动画持续1秒
anim.duration =1; 
//因为CGPoint是结构体，所以用NSValue包装成一个OC对象
anim.fromValue = [NSValue valueWithCGPoint:CGPointMake(50, 50)];
anim.toValue = [NSValue valueWithCGPoint:CGPointMake(100, 100)];
//通过MyAnim可以取回相应的动画对象，比如用来中途取消动画
[layer addAnimation:anim forKey:@"MyAnim"];
 
//缩放动画
CABasicAnimation *anim = [CABasicAnimation animationWithKeyPath:@"transform"];
//没有设置fromValue说明当前状态作为初始值
//宽度(width)变为原来的2倍，高度(height)变为原来的1.5倍
anim.toValue = [NSValuevalueWithCATransform3D:CATransform3DMakeScale(2, 1.5, 1)];
anim.duration = 1;
[layer addAnimation:anim forKey:nil];
//旋转动画
CABasicAnimation *anim = [CABasicAnimation animationWithKeyPath:@"transform"];
//这里是以向量(1, 1, 0)为轴，旋转π/2弧度(90&deg;)
//如果只是在手机平面上旋转，就设置向量为(0, 0, 1)，即Z轴
anim.toValue = [NSValuevalueWithCATransform3D:CATransform3DMakeRotation(M_PI_2, 1, 1, 0)];
anim.duration = 1;
[layer addAnimation:anim forKey:nil];
```

### CAKeyframeAnimation

CApropertyAnimation的子类，跟CABasicAnimation的区别是：

    CABasicAnimation只能从一个数值(fromValue)变到另一个数值(toValue)，而CAKeyframeAnimation会使用一个NSArray保存这些数值 .

属性解析：

    values：就是上述的NSArray对象。里面的元素称为”关键帧”(keyframe)。动画对象会在指定的时间(duration)内，依次显示values数组中的每一个关键帧 .

    path：可以设置一个CGPathRef\CGMutablePathRef,让层跟着路径移动。path只对CALayer的anchorPoint和position起作用。如果你设置了path，那么values将被忽略 .

    keyTimes：可以为对应的关键帧指定对应的时间点,其取值范围为0到1.0,keyTimes中的每一个时间值都对应values中的每一帧.当keyTimes没有设置的时候,各个关键帧的时间是平分的 .

    CABasicAnimation可看做是最多只有2个关键帧的CAKeyframeAnimation

    在关键帧动画中还有一个非常重要的参数,那便是calculationMode,计算模式.其主要针对的是每一帧的内容为一个座标点的情况,也就是对anchorPoint 和 position 进行的动画.当在平面座标系中有多个离散的点的时候,可以是离散的,也可以直线相连后进行插值计算,也可以使用圆滑的曲线将他们相连后进行插值计算. calculationMode目前提供如下几种模式：

    kCAAnimationLinear calculationMode的默认值,表示当关键帧为座标点的时候,关键帧之间直接直线相连进行插值计算;

     kCAAnimationDiscrete 离散的,就是不进行插值计算,所有关键帧直接逐个进行显示; 

     kCAAnimationPaced 使得动画均匀进行,而不是按keyTimes设置的或者按关键帧平分时间,此时keyTimes和timingFunctions无效; 

     kCAAnimationCubic 对关键帧为座标点的关键帧进行圆滑曲线相连后插值计算，这里的主要目的是使得运行的轨迹变得圆滑；

    kCAAnimationCubicPaced 看这个名字就知道和kCAAnimationCubic有一定联系,其实就是在kCAAnimationCubic的基础上使得动画运行变得均匀,就是系统时间内运动的距离相同,此时keyTimes以及timingFunctions也是无效的.

CAAnimationGroup

    CAAnimation的子类，可以保存一组动画对象，将CAAnimationGroup对象加入层后，组中所有动画对象可以同时并发运行.

属性解析：

    animations：用来保存一组动画对象的NSArray.

    默认情况下，一组动画对象是同时运行的，也可以通过设置动画对象的beginTime属性来更改动画的开始时间.
    

### CATransition

 CAAnimation的子类，用于做转场动画，能够为层提供移出屏幕和移入屏幕的动画效果。iOS比Mac OS X的转场动画效果少一点.

    UINavigationController就是通过CATransition实现了将控制器的视图推入屏幕的动画效果.

属性解析:

    type：动画过渡类型

    subtype：动画过渡方向

    startProgress：动画起点(在整体动画的百分比)

    endProgress：动画终点(在整体动画的百分比)

```
/* 过渡效果
  fade     //交叉淡化过渡(不支持过渡方向) kCATransitionFade
  push     //新视图把旧视图推出去  kCATransitionPush
  moveIn   //新视图移到旧视图上面   kCATransitionMoveIn
  reveal   //将旧视图移开,显示下面的新视图  kCATransitionReveal
  cube     //立方体翻滚效果
  oglFlip  //上下左右翻转效果
  suckEffect   //收缩效果，如一块布被抽走(不支持过渡方向)
  rippleEffect //滴水效果(不支持过渡方向)
  pageCurl     //向上翻页效果
  pageUnCurl   //向下翻页效果
  cameraIrisHollowOpen  //相机镜头打开效果(不支持过渡方向)
  cameraIrisHollowClose //相机镜头关上效果(不支持过渡方向)
 */    
 /* 过渡方向
  kCATransitionFromRight
  kCATransitionFromLeft
  kCATransitionFromBottom 
  kCATransitionFromTop
  */
// CATransition的使用 
CATransition *anim = [CATransition animation];
anim.type = @"cube"; // 动画过渡类型
anim.subtype = kCATransitionFromTop; // 动画过渡方向
anim.duration = 1; // 动画持续1s
// 代理，动画执行完毕后会调用delegate的animationDidStop:finished:
anim.delegate = self;
```

## 后记

另外还有 UIImageView的帧动画 和 UIActivityIndicatorView，这里就暂不介绍了。

参考文档  
[http://www.pocketdigi.com/20150105/1413.html](http://www.pocketdigi.com/20150105/1413.html)
[http://www.cnblogs.com/iCocos/p/4553123.html](http://www.cnblogs.com/iCocos/p/4553123.html)
[http://my.oschina.net/aofe/blog/270412](http://my.oschina.net/aofe/blog/270412)