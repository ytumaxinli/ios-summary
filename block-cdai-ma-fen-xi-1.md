#### **无外部变量block**

> #### **OC源代码**
>
> ```
> - (void)noDataBlockFunction {
>     
>     void (^noDataBlock)(void) = ^{
>         NSLog(@"无外部变量");
>     };
>     noDataBlock();
> }
> ```
>
> #### **C++代码**
>
> 编译后的noDataBlockFunction方法
>
> ```
> static void _I_BlockStructureViewController_noDataBlockFunction(BlockStructureViewController * self, SEL _cmd) 
> {
>     //调用block构造方法，得到block结构体指针
>     //参数1：函数指针：编译后block中的逻辑代码，被编译到了一个独立函数中，此函数指针即指向这个独立函数
>     //参数2：block结构体的desc结构体对象
>     void (*noDataBlock)(void) = ((void (*)())&__BlockStructureViewController__noDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__noDataBlockFunction_block_func_0, &__BlockStructureViewController__noDataBlockFunction_block_desc_0_DATA));
>     
>     //block回调方法函数指针的调用
>     ((void (*)(__block_impl *))((__block_impl *)noDataBlock)->FuncPtr)((__block_impl *)noDataBlock);
> }
> ```
>
> 编译后的block结构体
>
> ```
> struct __BlockStructureViewController__noDataBlockFunction_block_impl_0 {
>   //impl结构体
>   struct __block_impl impl;
>   
>   //desc结构体
>   struct __BlockStructureViewController__noDataBlockFunction_block_desc_0* Desc;
>
>     //block结构体构造方法
>   __BlockStructureViewController__noDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__noDataBlockFunction_block_desc_0 *desc, int flags=0) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
> ```
>
> 编译后的block回调方法
>
> ```
> //回调方法的参数为block结构体
> static void __BlockStructureViewController__noDataBlockFunction_block_func_0(struct __BlockStructureViewController__noDataBlockFunction_block_impl_0 *__cself) 
> {
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_0);
> }
> ```
>
> block的imp结构体
>
> ```
> struct __block_impl {
>   void *isa;
>   int Flags;      //与Block源码中Block_private.h中的枚举相对应。标示block是否被copy、是否为全局block、是否为堆block等
>   int Reserved;
>   void *FuncPtr;  //函数调用指针。编译后block中的逻辑代码被编译到了一个独立函数中，此函数指针用来指向和调用这个独立函数。
> };
> ```
>
> block的desc结构体
>
> ```
> static struct __BlockStructureViewController__noDataBlockFunction_block_desc_0 {
>   size_t reserved;
>   size_t Block_size;  //block结构体的大小，在复制时申请内存时使用
> } __BlockStructureViewController__noDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__noDataBlockFunction_block_impl_0)};
> ```
>
> **逻辑分析**
>
> 1、 noDataBlockFunction方法注册被到\_method\_list\_t中，注册名为\_I\_BlockStructureViewController\_noDataBlockFunction；
>
> ```
> //noDataBlockFunction编译后方法名为_I_BlockStructureViewController_noDataBlockFunction。此方法注册到了当前类的方法列表_method_list_t中，
>
> static struct /*_method_list_t*/ {
>     unsigned int entsize;  // sizeof(struct _objc_method)
>     unsigned int method_count;
>     struct _objc_method method_list[14];
> } _OBJC_$_INSTANCE_METHODS_BlockStructureViewController __attribute__ ((used, section ("__DATA,__objc_const"))) = {
>     ...
>     {(struct objc_selector *)"noDataBlockFunction", "v16@0:8", (void *)_I_BlockStructureViewController_noDataBlockFunction},
>     ...
> };
> ```
>
> 2、\_I\_BlockStructureViewController\_noDataBlockFunction方法调用
>
> ```
> static void _I_BlockStructureViewController_noDataBlockFunction(BlockStructureViewController * self, SEL _cmd) {
>
>     //3、获取block构造方法参数*fp,即回调方法函数指针，(void *)__BlockStructureViewController__noDataBlockFunction_block_func_0
>         
>     //4、获取block构造方法参数*desc。&__BlockStructureViewController__noDataBlockFunction_block_desc_0_DATA结构体地址
>     
>     //5、调用block构造方法得到block对象
>     void (*noDataBlock)(void) = ((void (*)())&__BlockStructureViewController__noDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__noDataBlockFunction_block_func_0, &__BlockStructureViewController__noDataBlockFunction_block_desc_0_DATA));
>     
>     //6、通过block结构体对象的函数指针调用回调方法
>     ((void (*)(__block_impl *))((__block_impl *)noDataBlock)->FuncPtr)((__block_impl *)noDataBlock);
> }
> ```
>
> 3、block回调方法\_\_BlockStructureViewController\_\_noDataBlockFunction\_block\_func\_0
>
> ```
> //block回调方法被编译到了此函数中
> static void __BlockStructureViewController__noDataBlockFunction_block_func_0(struct __BlockStructureViewController__noDataBlockFunction_block_impl_0 *__cself) {
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_0);
> }
> ```
>
> 4、生成block desc对象。\_\_BlockStructureViewController\_\_noDataBlockFunction\_block\_desc\_0
>
> ```
> //__BlockStructureViewController__noDataBlockFunction_block_desc_0_DATA成员值为
> //reserved = 0；
> //Block_size = sizeof(struct __BlockStructureViewController__noDataBlockFunction_block_impl_0)；
>
> static struct __BlockStructureViewController__noDataBlockFunction_block_desc_0 {
>   size_t reserved;
>   size_t Block_size;
> } __BlockStructureViewController__noDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__noDataBlockFunction_block_impl_0)};
> ```
>
> 5、block构造方法调用
>
> ```
> 5.1 构造方法实现
>   __BlockStructureViewController__noDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__noDataBlockFunction_block_desc_0 *desc, int flags=0) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
>
> 5.2 通过构造方法调用将参数赋值给对应的结构体成员
>     void (*noDataBlock)(void) = ((void (*)())&__BlockStructureViewController__noDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__noDataBlockFunction_block_func_0, &__BlockStructureViewController__noDataBlockFunction_block_desc_0_DATA));
>     赋值结果如下：
>     FuncPtr = __BlockStructureViewController__noDataBlockFunction_block_func_0；
>     Desc = &__BlockStructureViewController__noDataBlockFunction_block_desc_0_DATA
>     Flags = 0；//默认值
>
> 5.3 block结构体
> struct __BlockStructureViewController__noDataBlockFunction_block_impl_0 {
>   //impl结构体
>   struct __block_impl impl;
>   
>   //desc结构体
>   struct __BlockStructureViewController__noDataBlockFunction_block_desc_0* Desc;
>
>     //block结构体构造方法
>   __BlockStructureViewController__noDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__noDataBlockFunction_block_desc_0 *desc, int flags=0) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
> ```
>
> 6、调用block结构体中的函数指针FuncPtr即\_\_BlockStructureViewController\_\_emptyBlockFunction\_block\_func\_0。
>
> ```
> //参数为block对象
> static void __BlockStructureViewController__emptyBlockFunction_block_func_0(struct __BlockStructureViewController__emptyBlockFunction_block_impl_0 *__cself) 
> {
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_dd128d_mi_0);
> }
> ```



