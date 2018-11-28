###### Protocol

#### [UIViewControllerInteractiveTransitioning](https://developer.apple.com/documentation/uikit/uiviewcontrollerinteractivetransitioning?language=objc)

**简介**

> 一组能够使对象（如navigation controller）控制view controller转场的方法。
>
> _A set of methods that enable an object \(such as a navigation controller\) to drive a view controller transition._



**Declaration**

> @protocol UIViewControllerInteractiveTransitioning



**Overview**

> 一个遵循UIViewControllerInteractiveTransitioning协议的对象，能够通过加速、减速或者甚至反转view controller过渡的进度来响应触摸事件或者时变程序输入。例如：navigation controller上的交互式过渡可以通过移动view controller进入或者移除navigation 栈来响应滑动手势。
>
> 为了支持交互式view controller 过渡，还必须提供一个transition animator delegate。这个delegate是一个遵循[`UIViewControllerAnimatedTransitioning`](https://developer.apple.com/documentation/uikit/uiviewcontrolleranimatedtransitioning?language=objc) 协议的自定义对象。transition delegate（遵循UIViewControllerInteractiveTransitioning 协议的对象） 和 the transition animator（遵循UIViewControllerAnimatedTransitioning 协议的对象），能够并且你希望的话，可以被定义到同时遵循两个协议的的一个单独的自定义类中。
>
> 如果想要提供一个固定时长的view controller 转场动画-即不支持用户交互-单独使用transition animator即可。参见UIViewControllerAnimatedTransitioning
>
> 对于可以调用以从startInteractiveTransition：方法中检索视图转换上下文信息的方法，参见：[`UIViewControllerContextTransitioning`](https://developer.apple.com/documentation/uikit/uiviewcontrollercontexttransitioning?language=objc)
>
> .
>
>
>
> .



