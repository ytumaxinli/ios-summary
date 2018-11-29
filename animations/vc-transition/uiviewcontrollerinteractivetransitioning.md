###### Protocol

#### [UIViewControllerInteractiveTransitioning](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning?language=objc)

**简介**

> 一组使对象（例如navigation controller）能够驱动view controller转场的方法。
>
> _A set of methods that enable an object \(such as a navigation controller\) to drive a view controller transition._

**Declaration**

> @protocol UIViewControllerInteractiveTransitioning

**Overview**

> 一个遵循UIViewControllerInteractiveTransitioning协议的对象，能够通过加速、减速或者甚至反转view controller转场的进度来响应触摸事件或者通过编程输入的时间变量。例如：navigation controller上的交互式转场可以通过移动view controller进入或者移除navigation 栈来响应滑动手势。
>
> ```
> An interactive transition delegate (which is the term for an object that supports this protocol) can respond to touch events, or to time-varying programmatic input, by speeding up, slowing down, or even reversing the progress of a view controller transition. For example, an interactive transition on a navigation controller could respond to a swipe gesture by moving a view controller onto or off of the navigation stack.
> ```
>
> 为了支持交互式view controller转场，还必须提供一个transition animator delegate。这个delegate是一个遵循[`UIViewControllerAnimatedTransitioning`](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning?language=objc) 协议的自定义对象。transition delegate（遵循UIViewControllerInteractiveTransitioning 协议的对象） 和 the transition animator（遵循UIViewControllerAnimatedTransitioning 协议的对象），能够并且你希望的话，可以被定义到同时遵循两个协议的的一个单独的自定义类中。
>
> ```
> To support an interactive view controller transition, you must also provide a transition animator delegate, which is a custom object that adopts the UIViewControllerAnimatedTransitioning protocol. The transition delegate and the transition animator can, if you wish, be defined within a single custom class, but the class must adopt both protocols.
> ```
>
> 然而只是想要提供一个固定时长的view controller 转场动画-即不支持用户交互-单独使用transition animator即可。参见[UIViewControllerAnimatedTransitioning](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning?language=objc)
>
> ```
> If you instead want to provide a fixed-duration animated view controller transition—one that does not support user interaction—use a transition animator delegate on its own. Refer to UIViewControllerAnimatedTransitioning.
> ```
>
> 对于可以调用以从[startInteractiveTransition:](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning/1622028-startinteractivetransition?language=objc)方法中检索视图转换上下文信息的方法，参见： [UIViewControllerContextTransitioning](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning?language=objc)
>
> ```
> For the methods you can call to retrieve view transition context information from within your startInteractiveTransition: method, refer to UIViewControllerContextTransitioning.
> ```

**Topics**

> **Starting an Interactive Transition**
>
> > [**startInteractiveTransition:**](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning/1622028-startinteractivetransition?language=objc)
> >
> > ```
> > - (void)startInteractiveTransition:(id<UIViewControllerContextTransitioning>)transitionContext;
> > ```
> >
> > 当系统需要建立view controller的交互部分，并且启动动画时调用。_Called when the system needs to set up the interactive portions of a view controller transition and start the animations._
> >
> > [**wantsInteractiveStart**](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning/1829433-wantsinteractivestart?language=objc)
> >
> > 一个bool类型值，在启动时候标示过渡是否是可交互的。_A Boolean value indicating whether the transition is interactive when it starts._
>
> **Providing a Transition’s Completion Characteristics**
>
> > [**completionCurve**](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning/1622027-completioncurve?language=objc)
> >
> > 当系统需要动画完成曲线来进行交互式view controller过渡时调用。_Called when the system needs the animation completion curve for an interactive view controller transition._
> >
> > [**completionSpeed**](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning/1622031-completionspeed?language=objc)
> >
> > 在交互部分完成后，系统需要一个速度来完成交互式过渡时调用。Called when the system needs the speed at which to complete an interactive transition, after the interactive portion is finished.

**See Also**

> **Interactive Transitions**
>
> > [**UIPercentDrivenInteractiveTransition**](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition?language=objc)
> >
> > 控制两个view controller之间转场动画的对象。An object that drives an interactive animation between one view controller and another.
> >
> > [**UIViewImplicitlyAnimating**](https://developer.apple.com/documentation/uikit/uiviewimplicitlyanimating?language=objc)
> >
> > 修改正在运行的动画的接口。An interface for modifying an animation while it is running.[  ](https://developer.apple.com/documentation/uikit/uipercentdriveninteractivetransition?language=objc)



