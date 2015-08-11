AVI 动画绘图

Android

+ validate()

+ invalidate()

+ postInvalidate()


Android的界面显示

Window PhoneWindow DecorWindow

SurfaceFlinger

----

ActivityThread::handleLaunchActivity()


Android应用程序窗口的UI渲染过程可以分为测量、布局和绘制三个阶段。

Measure()->Layout()->Draw()

递归（深度优先）确定所有视图的大小（高、宽）
布局：递归（深度优先）确定所有视图的位置（左上角坐标）
绘制：在画布canvas上绘制应用程序窗口所有的视图

测量、布局没有太多要说的，这里要着重说一下绘制。Android目前有两种绘制模型：基于软件的绘制模型和硬件加速的绘制模型（从Android 3.0开始全面支持）。
 
在基于软件的绘制模型下，CPU主导绘图，视图按照两个步骤绘制：
1.      让View层次结构失效
2.      绘制View层次结构s



###iOS视图分层 WindowLevel

```
const UIWindowLevel UIWindowLevelNormal;
const UIWindowLevel UIWindowLevelAlert;
const UIWindowLevel UIWindowLevelStatusBar;
typedef CGFloat UIWindowLevel;

//打印结果
Normal window level: 0.000000
Alert window level: 2000.000000
Status window level: 1000.000000
```



参考

Android

[http://blog.csdn.net/qinjuning/article/details/7110211](http://blog.csdn.net/qinjuning/article/details/7110211)
