Protocol

#### [UIViewControllerTransitioningDelegate](https://developer.apple.com/documentation/uikit/uiviewcontrollertransitioningdelegate?language=objc)

**简介**

> 一组提供对象的方法，这些对象用来管理view controllers之间的固定时长转场或交互式转场。
>
> A set of methods that vend objects used to manage a fixed-length or interactive transition between view controllers.

**Declaration**

> @protocol UIViewControllerTransitioningDelegate

**Overview**

> 当使用custom modal presentation类型 present view controller时，需要设置[`modalPresentationStyle`](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621355-modalpresentationstyle?language=objc)属性为UIModalPresentationCustom,并且给[`transitioningDelegate`](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621421-transitioningdelegate?language=objc) 属性赋值一个遵循UIViewControllerTransitioningDelegate协议的对象。当present  view controller时，UIKit会从[`transitioningDelegate`](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621421-transitioningdelegate?language=objc) 查询那些将view controler动画到指定位置时使用的对象。
>
> ```
> When you want to present a view controller using a custom modal presentation type, set its modalPresentationStyle property to custom and assign an object that conforms to this protocol to its transitioningDelegate property. When you present that view controller, UIKit queries your transitioning delegate for the objects to use when animating the view controller into position.
> ```
>
> 当实现transitioning delegate时，可以根据view controller是正在被presented还是dismissed返回不同的动画对象。所有的转场都会使用一个转场动画对象—一个遵循[`UIViewControllerAnimatedTransitioning`](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning?language=objc) 协议的对象—来实现基础动画。转场动画对象在有限的时间段内执行一组动画。如果想要使用触摸输入或者其他用户交互来控制动画的时间帧，需要同时提供一个交互式动画对象—一个遵循[`UIViewControllerInteractiveTransitioning`](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning?language=objc) 协议的—来更新动画的进度。可以为view controller的presenting 和 dismissing提供单独的动画对象
>
> ```
> When implementing your transitioning delegate object, you can return different animator objects depending on whether a view controller is being presented or dismissed. All transitions use a transition animator object—an object that conforms to the UIViewControllerAnimatedTransitioning protocol—to implement the basic animations. A transition animator object performs a set of animations over a finite period of time. If you want to use touch input or other user interactions to control the timing of the animation, you can also provide an interactive animator object—an object that conforms to the UIViewControllerInteractiveTransitioning protocol—to update the progress of the animations. You can provide separate animator objects for presenting and dismissing the view controller.
> ```
>
> 对于custom modal transition类型，除了动画对象以外还可以提供一个[`UIPresentationController`](https://developer.apple.com/documentation/uikit/uipresentationcontroller?language=objc) 对象。系统在presenting view controller之前创建presentation controller，并且保存这个对象的引用直到view controller dismissed。因为presentation controller的存在超出了动画对象的生命周期，所以可以使用presentation controller来协调presentation 或 dismissal过程中难以做到的其他方面。例如，如果自定义过渡样式涉及将单独的阴影view显示为view controller’s content的背景，则presentation controller可以创建阴影view并显示它并在适当的时间隐藏它。
>
> ```
> For custom modal transition styles, you can provide a UIPresentationController object in addition to the animator objects. The system creates your presentation controller before presenting the view controller and keeps a reference to that object until the view controller is dismissed. Because its existence extends beyond the lifespan of either animator object, you can use the presentation controller to coordinate aspects of the presentation or dismissal process that would be difficult to do otherwise. For example, if your custom transition style involves displaying a separate shadow view as a backdrop to the view controller’s content, the presentation controller can create the shadow view and show it and hide it at the appropriate times.
> ```

**Topics**

> **Getting the Transition Animator Objects**
>
> > [**animationControllerForPresentedController:presentingController:sourceController:**](https://developer.apple.com/documentation/uikit/uiviewcontrollertransitioningdelegate/1622037-animationcontrollerforpresentedc?language=objc)
> >
> > ```
> > - (id<UIViewControllerAnimatedTransitioning>)animationControllerForPresentedController:(UIViewController *)presented 
> >                                                                   presentingController:(UIViewController *)presenting 
> >                                                                       sourceController:(UIViewController *)source;
> > ```
> >
> > 让代理对象返回过渡动画对象，当presenting view controller时使用。_ Asks your delegate for the transition animator object to use when presenting a view controller._
> >
> > [**animationControllerForDismissedController:**](https://developer.apple.com/documentation/uikit/uiviewcontrollertransitioningdelegate/1622047-animationcontrollerfordismissedc?language=objc)
> >
> > ```
> > - (id<UIViewControllerAnimatedTransitioning>)animationControllerForDismissedController:(UIViewController *)dismissed;
> > ```
> >
> > 让代理对象返回过渡动画对象，当dismissing view controller时使用。_Asks your delegate for the transition animator object to use when dismissing a view controller._
>
> **Getting the Interactive Animator Objects**
>
> > [**interactionControllerForPresentation:**](https://developer.apple.com/documentation/uikit/uiviewcontrollertransitioningdelegate/1622050-interactioncontrollerforpresenta?language=objc)
> >
> > ```
> > - (id<UIViewControllerInteractiveTransitioning>)interactionControllerForPresentation:(id<UIViewControllerAnimatedTransitioning>)animator;
> > ```
> >
> > 让代理对象返回交互式动画对象，当presenting view controller时使用。_Asks your delegate for the interactive animator object to use when presenting a view controller._
> >
> > [**interactionControllerForDismissal:**](https://developer.apple.com/documentation/uikit/uiviewcontrollertransitioningdelegate/1622030-interactioncontrollerfordismissa?language=objc)
> >
> > ```
> > - (id<UIViewControllerInteractiveTransitioning>)interactionControllerForDismissal:(id<UIViewControllerAnimatedTransitioning>)animator;
> > ```
> >
> > 让代理对象返回交互式动画对象，当dismissing view controller时使用。_Asks your delegate for the interactive animator object to use when dismissing a view controller._
>
> **Getting the Custom Presentation Controller**
>
> > [**presentationControllerForPresentedViewController:presentingViewController:sourceViewController:**](https://developer.apple.com/documentation/uikit/uiviewcontrollertransitioningdelegate/1622057-presentationcontrollerforpresent?language=objc)
> >
> > ```
> > - (UIPresentationController *)presentationControllerForPresentedViewController:(UIViewController *)presented 
> >                                                       presentingViewController:(UIViewController *)presenting 
> >                                                           sourceViewController:(UIViewController *)source;
> > ```
> >
> > 让代理对象返回一个自定义的presentation controller，在presenting a view controller时管理视图层次结构。_Asks your delegate for the custom presentation controller to use for managing the view hierarchy when presenting a view controller._



