从Xcode 8开始，LLVM已经支持[Objective-C](http://lib.csdn.net/base/objective-c)显式声明类属性了，这是为了与[Swift](http://lib.csdn.net/base/swift)中的类属性互操作而引入的。在[WWDC 2016 What’s New in LLVM](https://developer.apple.com/videos/play/wwdc2016/405/) 网页链接 里面有如图1这样一段话。

![](http://ww3.sinaimg.cn/mw690/c5ff030ejw1f83i4plo8oj2122052gnr.jpg "图1")

创建一个类属性很简单，主要有以下几个步骤：   
1. 使用@property \(class\)来声明一个类属性；   
2. 为类属性创建一个存储变量，通常为全局变量；   
3. 实现类属性的getter与setter方法，如果是只读属性，只需要实现getter方法。

具体实例如图2所示。

![](http://ww3.sinaimg.cn/mw690/c5ff030ejw1f83i4q8vv9j20it0ezju5.jpg "图2")

需要注意的是编译器不会自动帮我们生成类属性的getter和setter方法，所以2、3步是必须的。

实际上，在此之前，我们同样可以通过声明并实现obj的+obj与+setObj:方法，把obj当类属性并通过点语法来调用。而Xcode 8只是显式地支持去声明一个类属性。所以@property \(class\)与系统无关，在[iOS](http://lib.csdn.net/base/ios) 10之前的系统上运行也是没有问题的。

  
**参考链接**

> https://blog.csdn.net/sodaslay/article/details/75040165



