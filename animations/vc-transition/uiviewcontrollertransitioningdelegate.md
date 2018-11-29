Protocol

#### UIViewControllerTransitioningDelegate

**简介**

> 一组用来提供用于管理视图控制器之间的固定长度或交互式过渡的对象的方法。
>
> A set of methods that vend objects used to manage a fixed-length or interactive transition between view controllers.



**Declaration**

> @protocol UIViewControllerTransitioningDelegate



**Overview**

> 当使用custom modal presentation类型 present view controller时，需要设置[`modalPresentationStyle`](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621355-modalpresentationstyle?language=objc)属性为UIModalPresentationCustom,并且给[`transitioningDelegate`](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621421-transitioningdelegate?language=objc) 属性赋值一个遵循UIViewControllerTransitioningDelegate协议的对象。当present  view controller时，UIKit会从[`transitioningDelegate`](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621421-transitioningdelegate?language=objc) 查询那些将view controler动画到指定位置时使用的对象。
>
> 当实现transitioning delegate时，可以根据view controller是正在被presented还是dismissed返回不同的动画对象。所有的过渡都使用_transition animator _对象—一个遵循[`UIViewControllerAnimatedTransitioning`](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning?language=objc) 协议的对象—来实现基础动画。transition animator对象在有限的时间段内执行一组动画。如果想要使用触摸输入或者其他用户交互来控制动画的时间帧，需要同时提供一个_interactive animator_对象—一个遵循[`UIViewControllerInteractiveTransitioning`](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning?language=objc) 协议的—来更新动画的进度。可以为view controller的presenting 和 dismissing提供单独的动画对象
>
> 对于custom modal transition类型，除了动画对象以外还可以提供一个[`UIPresentationController`](https://developer.apple.com/documentation/uikit/uipresentationcontroller?language=objc) 对象。系统在presenting view controller之前创建presentation controller，并且保存这个对象的引用直到view controller dismissed。因为presentation controller的存在超出了动画对象的生命周期，所以可以使用presentation controller来协调presentation 或 dismissal过程中难以做到的其他方面。例如，如果自定义过渡样式涉及将单独的阴影view显示为view controller’s content的背景，则presentation controller可以创建阴影view并显示它并在适当的时间隐藏它。





