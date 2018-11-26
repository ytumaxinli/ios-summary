### Navigation Controller Views {#1654141}

> _A navigation controller is a container view controller—that is, it embeds the content of other view controllers inside of itself. You access a navigation controller’s view from its view property.  This view incorporates the navigation bar, an optional toolbar, and the content view corresponding to the topmost view controller._
>
> navigation controller 是一个容器view controller，它将其他控制器的内容嵌入到自身当中。我们可以通过view属性访问navigation controller's的view。这个view将navigation bar、可选的tool bar还有最上层的vc的view组织在一起，成为了navgiation contrller的view；
>
> 下图显示了如何组装这些视图以呈现整体导航界面,此图进一步将navigation interface嵌入到了一个tab bar interface中。![](/assets/NavigationViews.png)





关键属性：

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



