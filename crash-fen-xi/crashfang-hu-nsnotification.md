**Crash 产生原因**

> NSNotificationCenter 较之于 Delegate 可以实现更大的跨度的通信机制，可以为两个无引用关系的两个对象进行通信。NSNotification是iOS中一个调度消息通知的类，采用单例模式设计。因此，注册观察者后，没有在观察者dealloc时及时注销观察者，极有可能通知中心再发送通知时发送给僵尸对象而发生crash。
>
> 苹果在iOS9（iOS8.4也未发现crash问题）之后专门针对于这种情况做了处理，所以在iOS9之后，即使开发者没有移除observer，Notification crash也不会再产生了。不过针对于iOS9之前的用户，我们还是有必要做一下NSNotification Crash的防护。

**防护方案**

> **方案一**
>
> > 利用method swizzling hook NSObject的dealloc函数，在对象真正dealloc之前先调用一下\[\[NSNotificationCenter defaultCenter\] removeObserver:self\]即可。
> >
> > 注意到并不是所有的对象都需要做以上的操作，如果一个对象从来没有被NSNotificationCenter 添加为observer的话，在其dealloc之前调用removeObserver完全是多此一举。 所以我们hook了NSNotificationCenter的 addObserver:\(id\)observer selector:\(SEL\)aSelector name:\(NSString \*\)aName object:\(id\)anObject。函数，在其添加observer的时候，对observer动态添加标记flag。这样在observer dealloc的时候，就可以通过flag标记来判断其是否有必要调用removeObserver函数了。
> >
> > **此方案有以下缺点：**
> >
> > 1. ARC开发下，dealloc作为关键字，编译器是有所限制的。会产生编译错误“ARC forbids use of 'dealloc' in a @selector”。不过我们可以用运行时的方式进行解决。
> > 2. dealloc作为最为基础，调用次数最为频繁的方法之一。如对此方法进行替换，一是代码的引入对工程影响范围太大，二是执行的代价较大。因为大多数dealloc操作是不需要引入自动注销的，为了少数需求而对所有的执行都做修正是不适当的。
>
> **方案二**
> >



**参考文献**

> https://www.cnblogs.com/Xylophone/p/6394056.html



