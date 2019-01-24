#### **KVC取值原理**

> **流程描述：**方法的默认实现如下
>
> > 1 在调用者的类对象中依次寻找`-get<Key>, -<key>, or -is<Key>`方法。
> >
> > > 如果以上的任一方法被找到则会进行调用，调用结束后会对方法的返回结果进行处理
> > >
> > > 1.1 如果结果类型是对象类型，则将结果直接返回。
> > >
> > > 1.2 如果结果类型是NSNumber支持的标量类型\(NSInteger/char/int/BOOL等\)，则会将该结果转换成NSNumber类型的值进行返回。
> > >
> > > 1.3 如果结果类型是NSNumber不支持的标量类型\(CGRect/CGSize/CGPoint等\)，则会将该结果转化为NSValue类型的值进行返回。
>
> ![](/assets/KVC02.png)

#### **KVC设值的原理**

> ![](/assets/KVC01.png)

#### 问题

> ![](/assets/KVC03.png)



