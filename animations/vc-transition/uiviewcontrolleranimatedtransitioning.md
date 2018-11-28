#### [UIViewControllerAnimatedTransitioning](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning?language=objc)

> **简介**
>
> > 一组用于实现自定义视图控制器转场的动画的方法。_A set of methods for implementing the animations for a custom view controller transition._
>
> **Declaration**
>
> > @protocol UIViewControllerAnimatedTransitioning
>
> **Overview**
>
> > 此协议中的方法允许您定义动画对象，该动画对象创建动画，用于在固定时间内过渡view controller到屏幕上或屏幕外。使用协议创建的动画必须是不能交互的。要创建交互式过渡，必须将动画对象与控制动画时间的另一个对象组合在一起。
> >
> > ```
> > The methods in this protocol let you define an animator object, which creates the animations for transitioning a view controller on or off screen in a fixed amount of time. The animations you create using this protocol must not be interactive. To create interactive transitions, you must combine your animator object with another object that controls the timing of your animations.
> > ```
> >
> > 在动画对象中，通过实现[`transitionDuration:`](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning/1622032-transitionduration?language=objc)方法来指定转场的时长，并实现[`animateTransition:`](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning/1622061-animatetransition?language=objc)方法来自行创建动画。有关转场中涉及的对象的信息将以上下文对象的形式传递给animateTransition：方法。使用上下文提供的信息在指定的持续时间内将目标view controller's view 移入或移除屏幕。
> >
> > ```
> > In your animator object, implement the transitionDuration: method to specify the duration of your transition and implement the animateTransition: method to create the animations themselves. Information about the objects involved in the transition is passed to your animateTransition: method in the form of a context object. Use the information provided by that object to move the target view controller’s view on or off screen over the specified duration.
> > ```
> >
> > 通过转场代理创建animator对象 ，即创建一个遵循UIViewControllerTransitioningDelegate协议的对象。当显示一个view controller 通过设置presentation style [`UIModalPresentationCustom`](https://developer.apple.com/documentation/uikit/uimodalpresentationstyle/uimodalpresentationcustom?language=objc)，并且将转场代理设置为 view controller's 的[`transitioningDelegate`](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621421-transitioningdelegate?language=objc)属性。view controller通过转场代理找到动画对象，并使用它进行动画。可以为presenting 和 dismissing  view controller提供不同的动画。
> >
> > ```
> > Create your animator object from a transitioning delegate—an object that conforms to the UIViewControllerTransitioningDelegate protocol. When presenting a view controller, set the presentation style to UIModalPresentationCustom and assign your transitioning delegate to the view controller’s transitioningDelegate property. The view controller retrieves your animator object from the transitioning delegate and uses it to perform the animations. You can provide separate animator objects for presenting and dismissing the view controller.
> > ```
> >
> > 为了在view controller的转场中添加用户交互，必须将**动画对象**和一个**交互动画对象**（一个自定义遵循[`UIViewControllerInteractiveTransitioning`](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning?language=objc) 协议的对象）结合使用。对于更多定义交互转场的，查看[`UIViewControllerInteractiveTransitioning`](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning?language=objc)。
> >
> > ```
> > To add user interaction to a view controller transition, you must use an animator object together with an interactive animator object—a custom object that adopts the UIViewControllerInteractiveTransitioning protocol. For more on defining interactive transitions, see UIViewControllerInteractiveTransitioning.
> > ```
>
> **Topics**
>
> > **Performing a Transition**
> >
> > > [**animateTransition:**](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning/1622061-animatetransition?language=objc)
> > >
> > > `- (void)animateTransition:(id<UIViewControllerContextTransitioning>)transitionContext;`告诉动画对象执行转场动画。_Tells your animator object to perform the transition animations._
> > >
> > > [**animationEnded:**](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning/1622059-animationended?language=objc)  
> > > _// This is a convenience and if implemented will be invoked by the system when the transition context's completeTransition: method is invoked._
> > >
> > > `- (void)animationEnded:(BOOL)transitionCompleted;`告诉动画对象执行转场动画完毕。_Tells your animator object that the transition animations have finished._
> >
> > **Reporting Transition Duration**
> >
> > > [**transitionDuration:**](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning/1622032-transitionduration?language=objc)
> > >
> > > 设置转场动画的时长。_Asks your animator object for the duration \(in seconds\) of the transition animation._
> >
> > **Returning an Interruptible Animator**
> >
> > > [**interruptibleAnimatorForTransition:**](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning/1829434-interruptibleanimatorfortransiti?language=objc)
> > >
> > > Returns the interruptible animator to use during the transition.
>
> **See Also**
>
> > **Non-Interactive Transitions**
> >
> > > [**UIViewControllerContextTransitioning**](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning?language=objc)
> > >
> > > 一组为view controllers 间转场动画提供上下文信息的方法。_A set of methods that provide contextual information for transition animations between view controllers._



