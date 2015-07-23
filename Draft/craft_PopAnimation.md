# Pop Animation

参考 [https://github.com/MartinRGB/LearnCube-iOS](https://github.com/MartinRGB/LearnCube-iOS)

Profile添加
> pod 'pop', :git => 'https://github.com/facebook/pop.git'  
> or  
> pod 'pop', '~> 1.0'


Y向上弹入

```
CGPoint card2c=_card2.center;
    _card2.center=card2c;
    POPSpringAnimation * C2Y = [POPSpringAnimation animationWithPropertyNamed:kPOPLayerPositionY];
    C2Y.beginTime = CACurrentMediaTime() + 0.3;
    C2Y.toValue = @(card2c. y-= 200);
    C2Y.springBounciness = 4;
    C2Y.springSpeed = 8;
    [_card2 pop_addAnimation:C2Y forKey:@"Card2Y"];
    
    C2Y.completionBlock =^(POPAnimation *anim, BOOL finished){
    
```


```
POPBasicAnimation *C2A = [POPBasicAnimation animation];
    C2A.property = [POPAnimatableProperty propertyWithName:kPOPViewAlpha];
    C2A.beginTime = CACurrentMediaTime() + 0.3;
    C2A.fromValue= @(0);
    C2A.toValue= @(1);
    [_card2 pop_addAnimation:C2A forKey:@"C2A"];
    
```

画折线

```
UIBezierPath* bezier1Path = UIBezierPath.bezierPath;
        [bezier1Path moveToPoint: CGPointMake(50.68, 75.86)];
        [bezier1Path addLineToPoint: CGPointMake(86.61, 88.11)];
        [bezier1Path addLineToPoint: CGPointMake(129.68, 96.88)];
        [bezier1Path addLineToPoint: CGPointMake(172.45, 85.46)];
        [bezier1Path addLineToPoint: CGPointMake(222.67, 102)];
        [bezier1Path addLineToPoint: CGPointMake(256.5, 82.1)];
        bezier1Path.lineWidth = 2;
        [bezier1Path stroke];
        
        CAShapeLayer *line1=[CAShapeLayer layer];
        line1.path=bezier1Path.CGPath;
        line1.fillColor=[UIColor clearColor].CGColor;
        line1.strokeColor=[[UIColor colorWithRed: 0.868 green: 0.281 blue: 0.276 alpha: 1]CGColor];
        [self.card1.layer addSublayer:line1];
        
        
        CABasicAnimation *animation1=[CABasicAnimation animationWithKeyPath:@"strokeEnd"];
        animation1.beginTime =CACurrentMediaTime() + 0;
        animation1.duration=0.8;
        animation1.removedOnCompletion=NO;
        animation1.autoreverses = NO;
        animation1.fillMode=kCAFillModeBoth;
        animation1.fromValue=@(0);
        animation1.toValue=@(1);
        animation1.timingFunction=[CAMediaTimingFunction  functionWithControlPoints:0.5 : 0.29 :0.2 :0.83];
        [line1 addAnimation:animation1 forKey:@"line1"];

```




画圆环

```
    UIBezierPath* RingPath1 = UIBezierPath.bezierPath;
        [RingPath1 moveToPoint: CGPointMake(114.58, 113.91)];
        [RingPath1 addCurveToPoint: CGPointMake(84.5, 101) controlPoint1: CGPointMake(107.02, 105.96) controlPoint2: CGPointMake(96.34, 101)];
        [RingPath1 addCurveToPoint: CGPointMake(43, 142.5) controlPoint1: CGPointMake(61.58, 101) controlPoint2: CGPointMake(43, 119.58)];
    [RingPath1 stroke];
    
    CAShapeLayer *ring1=[CAShapeLayer layer];
    ring1.lineWidth = 15;
    ring1.path=RingPath1.CGPath;
    ring1.fillColor=[UIColor clearColor].CGColor;
    ring1.strokeColor=[[UIColor colorWithRed: 0.388 green: 0.748 blue: 0.741 alpha: 1]CGColor];
    [self.card2.layer addSublayer:ring1];
    
    CABasicAnimation *r1=[CABasicAnimation animationWithKeyPath:@"strokeEnd"];
        r1.beginTime =CACurrentMediaTime() ;
        r1.duration=0.2;
        r1.removedOnCompletion=NO;
        r1.autoreverses = NO;
        r1.fillMode=kCAFillModeBoth;
        r1.fromValue=@(0);
        r1.toValue=@(1);
        r1.timingFunction=[CAMediaTimingFunction  functionWithControlPoints:0.42 : 0 :1 :1];

```