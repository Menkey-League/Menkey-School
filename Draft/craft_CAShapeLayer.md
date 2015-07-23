# CAShapeLayer


普通CALayer在被初始化时是需要给一个frame值的,这个frame值一般都与给定view的bounds值一致,它本身是有形状的,而且是矩形.

CAShapeLayer在初始化时也需要给一个frame值,但是,它本身没有形状,它的形状来源于你给定的一个path,然后它去取CGPath值,它与CALayer有着很大的区别


CAShapeLayer有着几点很重要:

1. 它依附于一个给定的path,必须给与path,而且,即使path不完整也会自动首尾相接

2. strokeStart以及strokeEnd代表着在这个path中所占用的百分比

3. CAShapeLayer动画仅仅限于沿着边缘的动画效果,它实现不了填充效果


以下给出如何使用CAShapeLayer

    // 创建一个view
    UIView *showView = [[UIView alloc] initWithFrame:CGRectMake(100, 100, 100, 100)];
    [self.view addSubview:showView];
    showView.backgroundColor = [UIColor redColor];
    showView.alpha = 0.5;

 

    // 贝塞尔曲线(创建一个圆)
    UIBezierPath *path = [UIBezierPath bezierPathWithArcCenter:
    	CGPointMake(100 / 2.f, 100 / 2.f) 
    	radius:100 / 2.f 
    	startAngle:0 
    	endAngle:M_PI * 2 
    	clockwise:YES];


    // 创建一个shapeLayer
    CAShapeLayer *layer = [CAShapeLayer layer];
    layer.frame         = showView.bounds;                // 与showView的frame一致
    layer.strokeColor   = [UIColor greenColor].CGColor;   // 边缘线的颜色
    layer.fillColor     = [UIColor clearColor].CGColor;   // 闭环填充的颜色
    layer.lineCap       = kCALineCapSquare;               // 边缘线的类型
    layer.path          = path.CGPath;                    // 从贝塞尔曲线获取到形状
    layer.lineWidth     = 4.0f;                           // 线条宽度
    layer.strokeStart   = 0.0f;
    layer.strokeEnd     = 0.1f;

    // 将layer添加进图层
    [showView.layer addSublayer:layer];

 

    // 3s后执行动画操作(直接赋值就能产生动画效果)
    [[GCDQueue mainQueue] execute:^{
        layer.speed       = 0.1;
        layer.strokeStart = 0.5;
        layer.strokeEnd   = 0.9f;
        layer.lineWidth   = 1.0f;
    } afterDelay:NSEC_PER_SEC * 3];



    // 给这个layer添加动画效果
    CABasicAnimation *pathAnimation = [CABasicAnimation animationWithKeyPath:@"strokeEnd"];
    pathAnimation.duration = 1.0;
    pathAnimation.fromValue = [NSNumber numberWithFloat:0.5f];
    pathAnimation.toValue = [NSNumber numberWithFloat:0.8f];
    [layer addAnimation:pathAnimation forKey:nil]; 
 


    // 创建一个gradientLayer
    CAGradientLayer *gradientLayer =  [CAGradientLayer layer];
    gradientLayer.frame = showView.bounds;
    [gradientLayer setColors:[NSArray arrayWithObjects:
                               (id)[[UIColor redColor] CGColor],
                               (id)[[UIColor yellowColor] CGColor], nil]];
    [gradientLayer setLocations:@[@0.5,@0.9,@1]];
    [gradientLayer setStartPoint:CGPointMake(0.5, 1)];
    [gradientLayer setEndPoint:CGPointMake(0.5, 0)];