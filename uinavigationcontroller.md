### Navigation Controller Views {#1654141}

> _A navigation controller is a container view controller—that is, it embeds the content of other view controllers inside of itself. You access a navigation controller’s view from its view property.  This view incorporates the navigation bar, an optional toolbar, and the content view corresponding to the topmost view controller._
>
> navigation controller 是一个容器view controller，它将其他控制器的内容嵌入到自身当中。我们可以通过view属性访问navigation controller's的view。**navigation controller 的view只有一个，这个view将navigation bar、可选的tool bar还有最上层的vc的view组织在一起,来显示页面；**
>
> 下图显示了如何组装这些视图以呈现整体导航界面,此图进一步将navigation interface嵌入到了一个tab bar interface中。![](/assets/NavigationViews.png)
>
> **Note**
>
> > _Because the content view underlaps the navigation bar in iOS 7 and later, you must consider that space when designing your view controller content._
> >
> > 由于iOS7及之后的版本将view controller的内容view放到了navigation bar的下方\(被nav bar遮盖\)，因此当设计view controller 内容时必须要考虑这个间距

关键属性：

> **visibleViewController**
>
> > 如果有modal view controller\(无论是navigation controller present 还是 栈里的vc present的\)返回modal view controller，没有则返回栈顶的vc。
> >
> > _Return modal view controller if it exists. Otherwise the top view controller._

> #### interactivePopGestureRecognizer
>
> **Summary**
>
> The gesture recognizer responsible for popping the top view controller off the navigation stack.
>
> **Discussion**
>
> navigation controller 将手势添加到了它的view上，并使用手势将最上层的vc移出栈。可以通过这个属性找到界面中的手势识别器，并且将其它手势识别器的行为与此识别器关联起来。
>
> The navigation controller installs this gesture recognizer on its view and uses it to pop the topmost view controller off the navigation stack. You can use this property to retrieve the gesture recognizer and tie it to the behavior of other gesture recognizers in your user interface. When tying your gesture recognizers together, make sure they recognize their gestures simultaneously to ensure that your gesture recognizers are given a chance to handle the event.

> // Hide or show the navigation bar. If animated, it will transition vertically using UINavigationControllerHideShowBarDuration.  
> - \(void\)setNavigationBarHidden:\(BOOL\)hidden animated:\(BOOL\)animated;



