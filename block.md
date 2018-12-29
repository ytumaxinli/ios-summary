#### block类继承关系

> OC中的block其实是一个NSBlock的子类，按照block存储的内存区域不同可以分为:栈区block、堆区block和全局区block。它们的类继承关系如下：
>
> 栈区    ： \_\_NSStackBlock\_\_   -&gt; \_\_NSStackBlock   -&gt; NSBlock -&gt; NSObject
>
> 堆区    ：\_\_NSMallocBlock\_\_  -&gt; \_\_NSMallocBlock -&gt; NSBlock -&gt; NSObject
>
> 全局区： \_\_NSGlobalBlock\_    -&gt; \_\_NSGlobalBlock -&gt; NSBlock -&gt; NSObject

#### block类的结构

> 苹果block类源码结构
>
> ```
> struct Block_layout {
>     void *isa;                                  // isa指针 
>     volatile int32_t flags;                     // contains ref count
>     int32_t reserved;                         
>     void (*invoke)(void *, ...);                //函数指针，指向block体内逻辑代码对应的函数
>     struct Block_descriptor_1 *descriptor;      //block描述，包含block
>     // imported variables
> };
>
> struct Block_descriptor_1 {
>     uintptr_t reserved;
>     uintptr_t size;
> };
> ```
>
> clang编译后的C++ block类结构
>
> ```
> //block类结构
> struct __block_impl {
>   void *isa;
>   int Flags;
>   int Reserved;
>   void *FuncPtr;
> };
>
>
> //block对象结构
> struct __当前类名__block所在方法名_block_impl_0 {
>   struct __block_impl impl;
>   struct __当前类名__block所在方法名_block_desc_0* Desc;
>   
>   //block对象的构造方法
>   __当前类名__block所在方法名_block_impl_0(void *fp, struct __当前类名__block所在方法名_block_desc_0 *desc, int flags=0) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
> //block对象内部包含的逻辑代码
> static void __当前类名__block所在方法名_block_func_0(struct __当前类名__block所在方法名_block_impl_0 *__cself) {
>
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_dd128d_mi_0);
> }
> ```
>
> **总结：OC的block对象分为两部分: block结构体、block的回调方法**
>
> > **block结构体**
> >
> > 1. block类结构中的isa指针：指针指向\_NSConcreteStackBlock、\_NSConcreteMallocBlock等类。
> > 2. block类结构中的flags：用来标示block对象的引用计数等信息
> > 3. block类结构中的FuncPtr：此函数指针指向block的回调方法，回调方法在编译后成为一个独立函数
> > 4. block对象在某些情况下，会在block结构体中添加成员变量的方式，将block体内逻辑代码中所使用的外部变量引入自身内存。成员变量取得可能是外部变量的**值、地址、结构体**或者**当前类的self**等。此方式与自定义类添加成员变量的方式相同。
> > 5. oc中block方法的实现即调用此结构体的构造方法。
> >
> > **block的回调方法**
> >
> > 1. block回调方法编译后成为一个独立的函数体。
> > 2. 此函数体将block结构体的对象作为参数传递到函数体内
> > 3. 函数体通过block对象拿到外部变量的索引进行使用。



