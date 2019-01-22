#### OC对象的分类

> #### ![](/assets/OC内存分类.png)

#### 实例对象包含信息

> ![](/assets/instance内存结构.png)

#### 类对象包含信息

> #### ![](/assets/class内存结构.png)![](/assets/objc_class结构.png)

#### **元类对象包含信息**

> #### ![](/assets/meta-class内存结构.png)

#### **总结：**

> 1.instance对象存放：指向类的isa指针 和 对象成员变量的值（真正的值或者值的地址）。
>
> 2.class对象存放：指向元类的isa指针、指向父类的superclass指针（Class superclass）、类的对象方法信息（method\_list\_t）、类的属性信息（property\_list\_t）、类的成员变量信息（ivar\_list\_t） 和 类的协议信息（protocol\_list\_t），还有对象方法缓存（cache\_t cache）等
>
> 3.meta-class对象存放：指向根元类的isa指针、指向父类的superclass指针（Class superclass）、类方法信息（method\_list\_t）
>
> 4.class对象和meta-class对象都为Class类型即struct objc\_class \*结构体指针类型。因此它们的内存结构是一样的，只是有些有值有些没值。
>
> 5.一些方法的解释
>
> ```
> NSObject *object = [[NSObject alloc] init];
>
> id instanceClass = [object class];
> id classClass = [NSObject class];
> NSLog(@"%p  %@",instanceClass, instanceClass);
> NSLog(@"%p  %@",classClass   , classClass);
>
> id instanceClass2 = object_getClass(object);
> id classClass2 = object_getClass([object class]);
> NSLog(@"%p  %@",instanceClass2, instanceClass2);
> NSLog(@"%p  %@",classClass2   , classClass2);
>
> //打印结果
> 0x100b16140  NSObject
> 0x100b16140  NSObject
> 0x100b16140  NSObject
> 0x100b160f0  NSObject
> ```
>
> class方法 和 object\_getinstance方法内部实现
>
> ```
> + (Class)class {                    //类方法返回自身
>     return self;
> }
>
> - (Class)class {                    //实例方法返回isa指向的内容
>     return object_getClass(self);
> }
>
> Class object_getClass(id obj)       //返回isa指向的内容
> {
>     if (obj) return obj->getIsa();
>     else return Nil;
> }
> ```



