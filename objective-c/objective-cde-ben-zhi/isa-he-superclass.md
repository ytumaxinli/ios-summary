#### superclass指针

> ![](/assets/class_superclass.png)![](/assets/meta-class_superclass.png)

#### **isa指针**

> #### ![](/assets/isa指针.png) 
>
> #### ISA\_MASK
>
> > ![](/assets/isa_mask.png)
> >
> > 在新版本中isa所存放的地址并不直接是对应的类或者元类的地址，而是一个经过优化的指针。需要通过与ISA\_MASK安位“与”操作，得到真实的类或者元类的地址
> >
> > ```
> > //宏定义
> > # if __arm64__
> > #   define ISA_MASK        0x0000000ffffffff8ULL
> > # elif __x86_64__
> > #   define ISA_MASK        0x00007ffffffffff8ULL
> > # else
> > #   error unknown architecture for packed isa
> > # endif
> >
> > inline Class objc_object::ISA() 
> > {
> >     assert(!isTaggedPointer()); 
> > #if SUPPORT_INDEXED_ISA
> >     if (isa.nonpointer) {
> >         uintptr_t slot = isa.indexcls;
> >         return classForIndex((unsigned)slot);
> >     }
> >     return (Class)isa.bits;
> > #else
> >     return (Class)(isa.bits & ISA_MASK);
> > #endif
> > }
> > ```
> >
> > **示例**
> >
> > > ```
> > > NSObject *object = [[NSObject alloc] init];
> > > Class objectClass = [object class];
> > > Class metaClass = object_getClass(objectClass);
> > > //分别打印实例对象、类对象、元类对象的地址
> > > NSLog(@"%p  %p  %p",object, objectClass, metaClass);
> > >
> > > //打印结果
> > > 0x100e60c10  0x100b16140  0x100b160f0
> > > ```
> > >
> > > 通过lldb调试
> > >
> > > ```
> > > //按16进制输出实例对象的isa的值
> > > (lldb) p/x object->isa
> > > (Class) $1 = 0x001d800100b16141 NSObject            //发现isa中所存的地址与类对象objectClass的地址并不相同
> > >
> > > (lldb) p/x 0x001d800100b16141 & 0x00007ffffffffff8  //isa所存地址与ISA_MASK安位'与'操作此时返回值相同
> > > (long) $2 = 0x0000000100b16140
> > >
> > > (lldb) po 0x0000000100b16140                        //打印mask后的isasuo'zhi
> > > NSObject
> > > ```

#### 总结

> ![](/assets/isa_superclass.png)
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



