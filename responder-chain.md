#### **命中测试（**hit-test**）**

> 当iOS程序中发生触摸事件后，系统会将事件加入到UIApplication管理的一个任务队列中。UIApplication将处于任务队列最前端的事件向下分发到keyWindow。然后view层次结构中会通过命中测试来确定是哪一个view来响应事件。![](/assets/hit-test-depth-first-traversal.png)
>
> **命中测试对象**
>
> > 命中测试使用系统方法，在view或者继承自view的子控件上进行。
> >
> > ```
> > @interface UIView(UIViewGeometry)
> >     - (nullable UIView *)hitTest:(CGPoint)point withEvent:(nullable UIEvent *)event;   // recursively calls -pointInside:withEvent:. point is in the receiver's coordinate system
> >     - (BOOL)pointInside:(CGPoint)point withEvent:(nullable UIEvent *)event;   // default returns YES if point is in bounds
> > @end
> > ```
>
> **命中测试顺序**
>
> > 在不同深度的view结构中：从父view向子view进行深度遍历，当父view被命中后，会遍历其子view直到找到适合响应的子view或者返回自身。
> >
> > 在同深度的view结构中：从最后添加的子view进行反向遍历，即先遍历最后添加的子view，来减少遍历次数
>
> **命中测试默认条件**
>
> > 1. 当前view不能隐藏               :self.hidden == NO
> > 2. 当前view可以跟用户交互   :self.userInteractionEnabled == YES
> > 3. 当前view alpha值大于0.01 :self.alpha&gt;0.01
> > 4. 触摸点在当前view内           :\[self pointInside:point withEvent:event\] == YES
> >
> > ```
> > //返回视图层级中能响应触控点的最深视图
> > - (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
> >     if (!self.isUserInteractionEnabled || self.isHidden || self.alpha <= 0.01) {
> >         return nil;
> >     }
> >     if ([self pointInside:point withEvent:event]) {
> >         for (UIView *subview in [self.subviews reverseObjectEnumerator]) {
> >             CGPoint convertedPoint = [subview convertPoint:point fromView:self];
> >             UIView *hitTestView = [subview hitTest:convertedPoint withEvent:event];
> >             if (hitTestView) {
> >                 return hitTestView;
> >             }
> >         }
> >         return self;
> >     }
> >     return nil;
> > }
> >
> > //返回视图是否包含指定的某个点
> > -(BOOL)pointInside:(CGPoint)point withEvent:(UIEvent *)event
> > {
> >     return CGRectContainsPoint(self.frame, point);
> > }
> > ```
> >
> > **修改命中测试**
> >
> > > 有时我们想让view的区域范围外的点击事件也能命中改view，则可以通过修改命中测试方法来实现此功能
> > >
> > > ```
> > > - (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
> > >     if (!self.isUserInteractionEnabled || self.isHidden || self.alpha <= 0.01) {
> > >         return nil;
> > >     }
> > >     //将触发范围上下左右扩展各扩展5
> > >     CGRect touchRect = CGRectInset(self.bounds, -10, -10);
> > >     if (CGRectContainsPoint(touchRect, point)) {
> > >         for (UIView *subView in [self.subviews reverseObjectEnumerator]) {
> > >             CGPoint converPoint = [subView convertPoint:point fromView:self];
> > >             UIView *hitTestView = [subView hitTest:converPoint withEvent:event];
> > >             if (hitTestView) {
> > >                 NSLog(@"ViewB1 : hitTestView -> %@", [hitTestView class]);
> > >                 return hitTestView;
> > >             }
> > >         }
> > >         return self;
> > >     }
> > >     return nil;
> > > }
> > > ```
> >
> > **UIGestureRecognizer**
> >
> > > 当一个view添加了手势识别，则手势识别器会对该view和该view的所有子view进行命中测试。当子view被命中但是不响应事件时，会触发手势识别器的selector.**手势识别器不参与视图的响应者链。**



#### 事件（Events）

> **事件传递**
>
> > 在主事件循环中，应用程序对象在其事件队列中获取（原始）触摸事件，将它们打包为UIEvent对象中的UITouch对象，并将它们发送到发生触摸的视图窗口。 反过来，窗口对象将这些对象发送到此视图，这称为命中测试视图。 如果此视图无法处理触摸事件（通常是因为它尚未实现必需的事件处理方法），则事件会在响应程序链中向上移动，直到它被处理或丢弃为止。
> >
> > ![](/assets/event_delivery.jpg)
>
>
>
> **处理事件（Handle Events）**
>
> > 视图命中测试后，需要判断该视图是否能够处理事件。判断能否处理事件的方法是看视图是否**重写touches的四个方法。系统默认的实现是调用\[super touchesBegan:touches withEvent:event\],将事件转发给响应链。部分系统子控件已经重写了touches方法，如UIButton就是通过重写达到相应事件的能力。**
> >
> > [`touchesBegan:withEvent:`](https://developer.apple.com/documentation/uikit/uiresponder/1621142-touchesbegan)is called for touch objects in the Began phase.
> >
> > [`touchesMoved:withEvent:`](https://developer.apple.com/documentation/uikit/uiresponder/1621107-touchesmoved)is called for touch objects in the Moved phase.
> >
> > [`touchesEnded:withEvent:`](https://developer.apple.com/documentation/uikit/uiresponder/1621084-touchesended)is called for touch objects in the Ended phase
> >
> > [`touchesCancelled:withEvent:`](https://developer.apple.com/documentation/uikit/uiresponder/1621116-touchescancelled)is called when some external event—for example, an incoming phone call—causes the operating system to cancel touch objects in a multitouch sequence.

#### 

#### 响应链（Responder Chain）

> **简介**
>
> > 应用程序通过使用 responder 对象来接收和处理事件。 responder对象即UIResponder类或者其子类的实例，UIResponder的公共子类包括**UIView**，**UIViewController **和 **UIApplication.**
>
> **NextResponder**
>
> > View**: **如果视图是视图控制器的根视图，下一个响应器就是视图控制器;否则，下一个响应器就是视图的父视图
> >
> > UIViewController**:** 如果UIViewController的view是窗口root view，则下一个响应者是窗口对象; 如果视图控制器由另一个视图控制器呈现，则下一个响应者是呈现视图控制器.
> >
> > UIWindow**: **UIWindow的下一个响应者是UIApplication对象。
> >
> > UIApplication: 当app delegate是**UIResponder的实例**时下一个响应者是app delegate。其他实例则不行如View，ViewController或UIApplication对象本身。
> >
> > ![](/assets/ResponderChain.png)



