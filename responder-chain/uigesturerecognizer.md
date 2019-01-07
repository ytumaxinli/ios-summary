**具体手势识别器的基类**

#### **简介**

> 手势识别器对象 - 或简称为手势识别器 - 解耦用于识别触摸序列（或其他输入）的逻辑并对该识别起作用。当这些对象中的一个识别出共同的手势，或者在某些情况下，手势发生变化时，它会向每个指定的目标对象发送一个动作消息。
>
> UIGestureRecognizer的具体子类如下：
>
> * [`UITapGestureRecognizer`](https://developer.apple.com/documentation/uikit/uitapgesturerecognizer?language=objc)
>
> * [`UIPinchGestureRecognizer`](https://developer.apple.com/documentation/uikit/uipinchgesturerecognizer?language=objc)
>
> * [`UIRotationGestureRecognizer`](https://developer.apple.com/documentation/uikit/uirotationgesturerecognizer?language=objc)
>
> * [`UISwipeGestureRecognizer`](https://developer.apple.com/documentation/uikit/uiswipegesturerecognizer?language=objc)
>
> * [`UIPanGestureRecognizer`](https://developer.apple.com/documentation/uikit/uipangesturerecognizer?language=objc)
>
> * [`UIScreenEdgePanGestureRecognizer`](https://developer.apple.com/documentation/uikit/uiscreenedgepangesturerecognizer?language=objc)
>
> * [`UILongPressGestureRecognizer`](https://developer.apple.com/documentation/uikit/uilongpressgesturerecognizer?language=objc)
>
> UIGestureRecognizer类定义了一组可以为所有具体手势识别器配置的常见行为。它还可以与其委托\(采用UIGestureRecognizerDelegate协议的对象\)通信，从而支持对某些行为进行更细粒度的定制。**手势识别器对特定视图和该视图的所有子视图进行命中测试（hit-tested**）。因此，它必须与该视图相关联。要建立该关联，您必须调用UIView方法addGestureRecognizer：。** 手势识别器不参与视图的响应者链。**
>
> 手势识别器具有与其相关联的一个或多个目标 - 动作对。 如果存在多个目标 - 动作对，则它们是离散的，而不是累积的。识别手势导致向每个关联对的目标发送动作消息。 调用的操作方法必须符合以下签名之一`：- (void)handleGesture;`
>
> `- (void)handleGesture:(UIGestureRecognizer *)gestureRecognizer;`
>
> 符合后一签名的方法允许目标在某些情况下查询手势识别器发送消息以获得附加信息。例如，自上次调用此手势的动作方法以来，目标可以向UIRotationGestureRecognizer对象询问旋转角度（以弧度表示）。 手势识别器的客户端还可以通过调用locationInView：或locationOfTouch：inView：来询问手势的位置



