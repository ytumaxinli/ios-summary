**+initialize方法**

> +initialize方法会在类**第一次**接收到objc\_msgSend消息时调用
>
> > 通过递归遍历类的继承关系，先调用父类的+initialize，再调用子类的+initialize。
> >
> > 每个类只会通过+initialize方法初始化一次。调用前判断`flags& RW_INITIALIZE`，调用后置位`flags`标示，防止重复重复调用。
>
> +initialize方法通过**消息发送机制**调用—objc\_msgSend\(cls, SEL\_initialize\)
>
> > 当类别实现+initialize方法时优先调用类别+initialize方法。
> >
> > 子类方法列表中没有+initialize时会调用父类+initialize方法。
> >
> > 子类不实现initialize方法时，父类的+initialize方法会被多次调用。但是此时的调用是实现的子类初始化，而非父类多次初始化。



**源码流程**

> objc-msg-arm64.s  -&gt;  ENTRY \_objc\_msgSend                                       //消息发送入口
>
> CacheLookup                                                                                             //在类的方法缓存列表中查找
>
> CheckMiss                                                                                                  //第一次调用时缓存列表未找到
>
> ENTRY \_\_objc\_msgSend\_uncached                                                         //未找到缓存
>
> MethodTableLookup                                                                                 //去类的方法列表中查找
>
> objc-runtime-new.mm  -&gt;  \_class\_lookupMethodAndLoadCache3      //调用C方法
>
> lookUpImpOrForward\(cls, sel, obj,YES/\*initialize\*/, NO/\*cache\*/, YES/\*resolver\*/\);   //initialize=YES
>
> void \_class\_initialize\(Class cls\)                                                              //递归调用初始化
>
> void callInitialize\(Class cls\)                                                                    //objc\_msgSend\(cls, SEL\_initialize\)



**源码分析**

> \_class\_lookupMethodAndLoadCache3   消息发送机制入口函数，initialize=YES
>
> ```
> IMP _class_lookupMethodAndLoadCache3(id obj, SEL sel, Class cls)
> {
>     return lookUpImpOrForward(cls, sel, obj,YES/*initialize*/, NO/*cache*/, YES/*resolver*/);
> }
> ```
>
> lookUpImpOrForward   方法寻找和转发
>
> ```
> //消息转发时设置initialize==YES
> IMP lookUpImpOrForward(Class cls, SEL sel, id inst, 
>                        bool initialize, bool cache, bool resolver)
> {
>     ...
>     //该类没有调用过+initialize方法时，调用_class_initialize
>     if (initialize  &&  !cls->isInitialized()) {
>         runtimeLock.unlock();
>         //当cls为元类时获取与元类对应的类
>         _class_initialize (_class_getNonMetaClass(cls, inst));
>         runtimeLock.lock();
>     }
>     ...
> }
>
> ```
>
> \_class\_initialize   递归遍历cls父类
>
> ```
> void _class_initialize(Class cls)
> {
> ...
>     supercls = cls->superclass;
>     //如果cls有父类，且父类没有+initialize过。递归调用
>     if (supercls  &&  !supercls->isInitialized()) {
>         //递归调用
>         _class_initialize(supercls);
>     }
> ...
> @try
>     {
>         callInitialize(cls);
>     }
>     @catch (...) {
>         ...
>     }
>     @finally
>     {
>         // 设置该类flag标示该类已经调用+initialize初始化过
>         lockAndFinishInitializing(cls, supercls);
>     }
> ...
> }
> ```
>
> callInitialize\(Class cls\) 通过消息机制调用initialize方法。
>
> ```
> void callInitialize(Class cls)
> {
>   ((void(*)(Class,SEL))objc_msgSend)(cls, SEL_initialize);
> }
> ```
>
> （**消息发送机制：**遍历类的继承关系，依次从子类到父类的方法缓存列表和方法列表中寻找方法，未找到时进行动态方法解析，未能解析是走消息转发机制）



**load、initialize方法的区别什么**

> 调用方式
>
> > load是根据函数地址直接调用
> >
> > initialize是通过objc\_msgSend调用
>
> 调用时刻
>
> > load是runtime加载类、分类的时候调用（只会调用1次）
> >
> > initialize是类第一次接收到消息的时候调用，每一个类只会initialize一次（父类的initialize方法可能会被调用多次）
>
> load、initialize的调用顺序
>
> > load方法
> >
> > > 先调用类的load
> > >
> > > > 先编译的类，优先调用load
> > > >
> > > > 调用子类的load之前，会先调用父类的load
> > >
> > > 再调用分类的load
> > >
> > > > 先编译的分类，优先调用load
> >
> > initializefa你敢发
> >
> > > 先初始化父类
> > >
> > > 再初始化子类（可能最终调用的是父类的initialize方法）



**示例**

> ```
> @interface Person : NSObject
>
> @end
> @implementation Person
>
> + (void)initialize
> {
>     NSLog(@"Person +initialize");
> }
>
> @end
>
> @interface Student : Person
>
> @end
> @implementation Student
>
> @end
>
> @interface Teachaer : Person
>
> @end
> @implementation Teachaer
>
> @end
>
> [Person alloc];
> [Student alloc];
> [Teachaer alloc];
>
> //打印结果
> Person +initialize
> Person +initialize
> Person +initialize
> ```





