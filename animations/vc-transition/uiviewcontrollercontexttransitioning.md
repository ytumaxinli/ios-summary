###### **Protocol**

#### [UIViewControllerContextTransitioning](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning?language=objc)

> #### **简介**
>
> > 为页面View Controller间**转场动画**，提供上下文信息的一组方法。
> >
> > _A set of methods that provide contextual information for transition animations between view controllers_
>
> **Declaration**
>
> > @protocol UIViewControllerContextTransitioning
>
> **Overview**
>
> > 不要在自己的类中遵循此协议，也不要直接创建遵循此协议的对象。在转场期间，转场所涉及的动画对象从UIKit接收完全配置的上下文对象。自定义动画对象—遵循UIViewControllerAnimatedTransitioning或者UIViewControllerInteractiveTransitioning协议的对象—应该只是从上下文对象中检索所需的信息。
> >
> > ```
> > Do not adopt this protocol in your own classes, nor should you directly create objects that adopt this protocol. During a transition, the animator objects involved in that transition receive a fully configured context object from UIKit. Custom animator objects—objects that adopt the UIViewControllerAnimatedTransitioning or UIViewControllerInteractiveTransitioning protocol—should simply retrieve the information they need from the provided object.
> > ```
> >
> > 上下文对象封装有关转换中涉及的views和view controllers的信息。它也包含了怎样执行转场的详细信息。对于交互型转场，**交互动画对象使用这个协议的方法来报告动画的进度**。在动画开始时，交互动画对象必须保存一个上下文的指针。根据用户的交互，动画对象然后调用[`updateInteractiveTransition:`](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622025-updateinteractivetransition?language=objc),[`finishInteractiveTransition`](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622056-finishinteractivetransition?language=objc) or [`cancelInteractiveTransition`](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622038-cancelinteractivetransition?language=objc) 来报告动画完成的进度。这些方法给UIKit发送信息，因此UIKit就可以驱动动画的时间进度。
> >
> > ```
> > A context object encapsulates information about the views and view controllers involved in the transition. It also contains details about the how to execute the transition. For interactive transitions, the interactive animator object uses the methods of this protocol to report the animation’s progress. When the animation starts, the interactive animator object must save a pointer to the context object. Based on user interactions, the animator object then calls the updateInteractiveTransition:, finishInteractiveTransition, or cancelInteractiveTransition methods to report the progress toward completing the animation. Those methods send that information to UIKit so that it can drive the timing of the animations.
> > ```
> >
> > **重要**
> >
> > 定义自定义动画对象时，需要始终检查isAnimated方法返回的值，以确定是否应创建动画。并且在创建转场动画时，需要在合适的完成block中调用[`completeTransition:`](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622042-completetransition?language=objc) 方法，让UIKit知道所有的动画已经执行完。
> >
> > ```
> > When defining custom animator objects, always check the value returned by the isAnimated method to determine whether you should create animations at all. And when you do create transition animations, always call the completeTransition: method from an appropriate completion block to let UIKit know when all of your animations have finished.
> > ```
>
> **Topics**
>
> > **Accessing the Transition Objects  **
> >
> > > [**containerView**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622045-containerview?language=objc)
> > >
> > > 扮演在转场中涉及到的views的父view. _The view that acts as the superview for the views involved in the transition_**.**
> > >
> > > [**viewControllerForKey:**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622043-viewcontrollerforkey?language=objc)
> > >
> > > 返回transition中涉及的view congtroller。_Returns a view controller involved in the transition**.**_
> > >
> > > ```
> > > [transitionContext viewControllerForKey:UITransitionContextFromViewControllerKey];
> > > [transitionContext viewControllerForKey:UITransitionContextToViewControllerKey];
> > > ```
> > >
> > > [**viewForKey:**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622055-viewforkey?language=objc)
> > >
> > > 返回transition中涉及的指定view。_Returns the specified view involved in the transition._
> > >
> > > ```
> > > [transitionContext viewForKey:UITransitionContextFromViewKey];
> > > [transitionContext viewForKey:UITransitionContextToViewKey];
> > > ```
> >
> > **Getting the Transition Frame Rectangles**
> >
> > > [**initialFrameForViewController:**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622033-initialframeforviewcontroller?language=objc)
> > >
> > > `- (CGRect)initialFrameForViewController:(UIViewController *)vc;`
> > >
> > > 返回指定的view controller's view的起始frame。_ Returns the starting frame rectangle for the specified view controller’s view. _
> > >
> > > [**finalFrameForViewController:**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622024-finalframeforviewcontroller?language=objc)
> > >
> > > `- (CGRect)finalFrameForViewController:(UIViewController *)vc;`
> > >
> > > 返回指定的view controller’s view的最终frame。_Returns the ending frame rectangle for the specified view controller’s view._
> >
> > **Getting the Transition Behaviors**
> >
> > > [**animated**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/2097569-animated?language=objc)
> > >
> > > 一个bool类型值，指示这个转场是否应该被设置为动画。_A Boolean value indicating whether the transition should be animated_**.**
> > >
> > > [**interactive**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/2097570-interactive?language=objc)
> > >
> > > 一个bool类型值，指示这个转场正在交互。_A Boolean value indicating whether the transition is currently interactive. _
> > >
> > > [**presentationStyle**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622049-presentationstyle?language=objc)
> > >
> > > 返回view controller转场的presentation style。_Returns the presentation style for the view controller transition._
> >
> > **Reporting the Transition Progress**
> >
> > > [**completeTransition:**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622042-completetransition?language=objc)
> > >
> > > `- (void)completeTransition:(BOOL)didComplete;`
> > >
> > > 通知系统transition动画已经完成。_Notifies the system that the transition animation is done._
> > >
> > > [**updateInteractiveTransition:**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622025-updateinteractivetransition?language=objc)
> > >
> > > `- (void)updateInteractiveTransition:(CGFloat)percentComplete;`
> > >
> > > 更新transition动画完成的百分比。_Updates the completion percentage of the transition._
> > >
> > > [**pauseInteractiveTransition**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1829437-pauseinteractivetransition?language=objc)
> > >
> > > `- (void)pauseInteractiveTransition;`
> > >
> > > 告诉系统暂停动画。_Tells the system to pause the animations._
> > >
> > > [**finishInteractiveTransition**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622056-finishinteractivetransition?language=objc)
> > >
> > > 通知系统用户交互transition完成。_Notifies the system that user interactions signaled the completion of the transition._
> > >
> > > [**cancelInteractiveTransition**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622038-cancelinteractivetransition?language=objc)
> > >
> > > 通知系统用户交互transition取消。_Notifies the system that user interactions canceled the transition._
> > >
> > > [**transitionWasCancelled**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622039-transitionwascancelled?language=objc)   
> > >
> > > 返回一个bool值，指示转场是否被取消。_Returns a Boolean value indicating whether the transition was canceled._
> >
> > **Getting the Rotation Factor**
> >
> > > [**targetTransform**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning/1622036-targettransform?language=objc)
> > >
> > > `@property(nonatomic, readonly) CGAffineTransform targetTransform;`
> > >
> > > 返回一个仿射矩阵，标示在transition中应用的旋转量。Returns a transform indicating the amount of rotation being applied during the transition.
> >
> > **Constants**
> >
> > > [**UITransitionContextViewControllerKey**](https://developer.apple.com/documentation/uikit/uitransitioncontextviewcontrollerkey?language=objc)
> > >
> > > 用来识别transition中涉及的view controller的key值。参见 [**viewControllerForKey:**](#) 。_The keys you use to identify the view controllers involved in a transition._
> > >
> > > [**UITransitionContextViewKey**](https://developer.apple.com/documentation/uikit/uitransitioncontextviewkey?language=objc)
> > >
> > > 用来识别transition中涉及的view的key值。参见[**viewForKey:**](#)。_The keys you use to identify the views involved in a transition._
>
> **See Also**
>
> > **Non-Interactive Transitions：  无交互Transition**
> >
> > > [**UIViewControllerAnimatedTransitioning**](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning?language=objc)
> > >
> > > 一组用于实现自定义视图控制器转换的动画的方法。_A set of methods for implementing the animations for a custom view controller transition._

[                                
](https://developer.apple.com/documentation/uikit/uitransitioncontextviewcontrollerkey?language=objc)

