#### **通过案例探究KVO原理**

> ```
> - (void)viewDidLoad {
>     [super viewDidLoad];
>     
>     self.person = [[MJPerson alloc] init];
>     NSLog(@"isa:%@  cls->superclass:%@ class:%@  superclass:%@", object_getClass(self.person), class_getSuperclass(object_getClass(self.person)), [self.person class], [self.person superclass]);
>     
>     //给person对象添加KVO监听
>     [self.person addObserver:self forKeyPath:@"age" options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld context:@"123"];
>     
>     NSLog(@"isa:%@  cls->superclass:%@ class:%@  superclass:%@", object_getClass(self.person), class_getSuperclass(object_getClass(self.person)), [self.person class], [self.person superclass]);
> }
>
>
> //打印打印结果
> isa:MJPerson                 cls->superclass:NSObject class:MJPerson  superclass:NSObject
> isa:NSKVONotifying_MJPerson  cls->superclass:MJPerson class:MJPerson  superclass:NSObject
>
> ```
>
> **总结：**通过以上打印结果发现
>
> ① 成员变量添加KVO前后isa指针指向的类不同。可以推断出添加KVO时runtime动态添加了一个新类，类型格式为NSKVONotifying\_原类名
>
> ② 成员变量添加KVO前后类结构体中superclass指针指向的类不同，动态增加的类的superclass指向原类MJPerson。可以推断出新增类NSKVONotifying\_MJPerson 是原类MJPerson 的子类。
>
> ③ 成员变量添加KVO前后调用class方法，返回的类名同。由于实例方法- \(Class\)class在根类NSObject的实现是通过isa获取对应的类，在①中发现isa执行的类是不同的，而此时返回的类却是相同的可以推断出NSKVONotifying\_MJPerson类重写了- \(Class\)class方法，并没有调用根类中的方法
>
> ④ 成员变量添加KVO前后调用superclass方法，返回的类名同。同理③可以推断出NSKVONotifying\_MJPerson类重写了- \(Class\)superclass方法，并没有调用根类中的方法。

![](/assets/KVO01.png)![](/assets/KVO02.png)

