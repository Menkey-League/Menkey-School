# AVI系列 触摸传递 动画机制

## 响应事件

他们的响应事件也是如UIView一样，由UIResponder负责，比如，单指点击，滑动，缩放等，而其中则是由The Responder Chain负责各种交互在不同层次的传递与判断由谁响应。