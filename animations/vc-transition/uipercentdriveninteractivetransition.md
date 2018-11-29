###### Class

#### [UIPercentDrivenInteractiveTransition](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition?language=objc)

**简介**

> 驱动两个view controller之间交互动画的对象
>
> An object that drives an interactive animation between one view controller and another.

**Declaration**

> @interface UIPercentDrivenInteractiveTransition : NSObject

**Overview**

> 百分比驱动交互式转场对象依赖 transition animator delegate--即一个遵循[`UIViewControllerAnimatedTransitioning`](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning?language=objc)协议的自定义对象--来创建动画和执行动画。
>
> ```
> A percent-driven interactive transition object relies on a transition animator delegate—a custom object that adopts the UIViewControllerAnimatedTransitioning protocol—to set up and perform the animations.
> ```
>
> 为了使用这个具体的类，在view controller代理请求交互式转场控制器的时候，返回一个UIPercentDrivenInteractiveTransition的实例。当影响转场进度的用户事件到达时，需要调用[`updateInteractiveTransition:`](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1622051-updateinteractivetransition?language=objc),[`cancelInteractiveTransition`](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1622026-cancelinteractivetransition?language=objc), and [`finishInteractiveTransition`](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1622035-finishinteractivetransition?language=objc)方法以反映当前进度。例如：可以通过手势识别的代理方法，调用这些方法以反映手势完成了多少。
>
> ```
> To use this concrete class, return an instance of it from your view controller delegate when asked for an interactive transition controller. As user events arrive that would affect the progress of a transition, call the updateInteractiveTransition:, cancelInteractiveTransition, and finishInteractiveTransition methods to reflect the current progress. For example, you might call these methods from a gesture recognizer to reflect how much of the gesture is completed.
> ```
>
> 如果使用UIPercentDrivenInteractiveTransition的子类，必须在每个覆盖方法的开始调用父类方法。

**Topics**

> **Accessing Transition Attributes**
>
> > [**timingCurve**](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1829439-timingcurve?language=objc)
> >
> > 时间曲线，在驱动动画的时候使用。The timing curve to use when driving the animations.
> >
> > [**completionCurve**](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1622048-completioncurve?language=objc)
> >
> > 指示交互式过渡的动画完成曲线。Indicates the animation completion curve for an interactive transition.
> >
> > [**duration**](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1622029-duration?language=objc)
> >
> > 过渡动画的总持续时间（以秒为单位）。The overall duration \(in seconds\) of the transition animation.
> >
> > [**percentComplete**](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1622053-percentcomplete?language=objc)
> >
> > 过渡完成的百分比。The amount of the transition \(specified as a percentage of the overall duration\) that is complete.
> >
> > [**completionSpeed**](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1622052-completionspeed?language=objc)
> >
> > 过渡动画的速度。The speed of the transition animation.
> >
> > [**wantsInteractiveStart**](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1829427-wantsinteractivestart?language=objc)
> >
> > 一个布尔值，指示动画是否初始化为交互式。A Boolean value indicating whether the animations are interactive initially.
>
> **Managing a Transition**
>
> > [**- updateInteractiveTransition:**](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1622051-updateinteractivetransition?language=objc)
> >
> > 更新过渡完成的百分比。Updates the completion percentage of the transition.
> >
> > [**- pauseInteractiveTransition**](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1829435-pauseinteractivetransition?language=objc)
> >
> > 暂停一个可中断的过渡动画。Pauses an interruptible transition animation.
> >
> > [**- cancelInteractiveTransition**](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1622026-cancelinteractivetransition?language=objc)
> >
> > 通知系统用户交互取消了这个过渡。Notifies the system that user interactions canceled the transition.
> >
> > [**- finishInteractiveTransition**](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition/1622035-finishinteractivetransition?language=objc)
> >
> > 通知系统用户交互表示过渡完成。Notifies the system that user interactions signaled the completion of the transition.



