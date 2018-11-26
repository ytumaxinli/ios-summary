关键属性：

> #### interactivePopGestureRecognizer
>
> **Declaration**
>
> @property\(nonatomic, readonly\) UIGestureRecognizer \*interactivePopGestureRecognizer;
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







