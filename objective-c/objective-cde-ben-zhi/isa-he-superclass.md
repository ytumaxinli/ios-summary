#### superclass指针

> ![](/assets/class superclass.png)![](/assets/meta-class superclass.png)

#### **isa指针**

> #### ![](/assets/isa指针.png)

#### 总结

> ![](/assets/isa superclass总结.png)
>
> **在类方法调用的过程中，未找到对应的类方法时会类的继承关系中的父类的方法列表中寻找。由于root meta-class的superclass是root class\(一般为NSObject\)，由于root class中存储的是对象方法，因此对类方法的调用有可能会调用到根类中的对象方法。示例如下：**
>
> ```
> @interface NSObject (Extends)
> - (void)callFunction;
> @end
> @implementation NSObject (Extends)
> - (void)callFunction
> {
>     NSLog(@"did callFunction");
> }
> @end
>
> @interface Person : NSObject
> @end
> @implementation Person
> @end
>
> //此类方法的调用会调用到NSObject+Extends中的对象方法- (void)callFunction
> [Person performSelector:@selector(callFunction) withObject:nil];
>
>
> //打印日志
> did callFunction
> ```



