#### UIViewControllerAnimatedTransitioning

> **简介**
>
> > 一组用于实现自定义视图控制器转场的动画的方法。_A set of methods for implementing the animations for a custom view controller transition._
>
>
>
> **Declaration**
>
> > @protocol UIViewControllerAnimatedTransitioning
>
>
>
> **Overview**
>
> > 此协议中的方法允许您定义动画对象，该动画对象创建一个用于屏幕上或屏幕外的view controller固定时长的转场动画。使用协议创建的动画必须是不能交互的。要创建交互式过渡，必须将动画对象与控制动画时间的另一个对象组合在一起。
> >
> > 在动画对象中，通过实现[`transitionDuration:`](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning/1622032-transitionduration?language=objc)方法来指定转场的时长，并实现[`animateTransition:`](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning/1622061-animatetransition?language=objc)方法来自行创建动画。有关转场中涉及的对象的信息将以上下文对象的形式传递给animateTransition：方法。使用上下文提供的信息在指定的持续时间内将目标view controller's view 移入或移除屏幕。
> >
> > 通过转场代理创建animator对象 ，即创建一个遵循UIViewControllerTransitioningDelegate协议的对象。当显示一个view controller 通过设置presentation style [`UIModalPresentationCustom`](https://developer.apple.com/documentation/uikit/uimodalpresentationstyle/uimodalpresentationcustom?language=objc)，并且将转场代理设置为 view controller's 的[`transitioningDelegate`](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621421-transitioningdelegate?language=objc)属性。view controller通过转场代理找到动画对象，并使用它进行动画。可以为presenting 和 dismissing  view controller提供不同的动画。
> >
> > 为了在view controller的转场中添加用户交互，必须将**动画对象**和一个**交互动画对象**（一个自定义遵循[`UIViewControllerInteractiveTransitioning`](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning?language=objc) 协议的对象）结合使用。对于更多定义交互转场的，查看[`UIViewControllerInteractiveTransitioning`](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning?language=objc)。



