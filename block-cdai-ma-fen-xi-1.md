**无参数block**

> **OC源代码**
>
> ```
> - (void)emptyBlockFunction {
>     
>     void (^emptyBlock)(void) = ^{
>         NSLog(@"八点钟学院");
>     };
>     emptyBlock();
> }
> ```
>
> **C++代码**
>
> ```
> //编译后的emptyBlockFunction方法
> static void _I_BlockStructureViewController_emptyBlockFunction(BlockStructureViewController * self, SEL _cmd) 
> {
>     //通过调用构造方法__BlockStructureViewController__emptyBlockFunction_block_impl_0，得到emptyBlock函数指针。
>     //参数1：函数指针：编译后block中的逻辑代码，被编译到了一个独立函数中，此函数指针即指向这个独立函数
>     //参数2：__BlockStructureViewController__emptyBlockFunction_block_desc_0_DATA结构体地址，此结构体存储了emptyBlock为结构体的size
>     void (*emptyBlock)(void) = ((void (*)())&__BlockStructureViewController__emptyBlockFunction_block_impl_0((void *)__BlockStructureViewController__emptyBlockFunction_block_func_0, &__BlockStructureViewController__emptyBlockFunction_block_desc_0_DATA));
>     
>     //调用block方法，即调用参数1传递的函数
>     ((void (*)(__block_impl *))((__block_impl *)emptyBlock)->FuncPtr)((__block_impl *)emptyBlock);
> }
>
> //转化后的emptyBlock为结构体
> struct __BlockStructureViewController__emptyBlockFunction_block_impl_0 
> {
>   //当前结构体的内容
>   struct __block_impl impl;
>   //当前结构体的描述
>   struct __BlockStructureViewController__emptyBlockFunction_block_desc_0* Desc;
>   
>   //结构体构造函数
>   __BlockStructureViewController__emptyBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__emptyBlockFunction_block_desc_0 *desc, int flags=0) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
> struct __block_impl {
>   void *isa;
>   int Flags;      //与Block源码中Block_private.h中的枚举相对应。标示block是否被copy、是否为全局block、是否为堆block等
>   int Reserved;
>   void *FuncPtr;  //函数调用指针。编译后block中的逻辑代码被编译到了一个独立函数中，此函数指针用来指向和调用这个独立函数。
> };
>
> static struct __BlockStructureViewController__emptyBlockFunction_block_desc_0 
> {
>   size_t reserved;
>   size_t Block_size;
> } __BlockStructureViewController__emptyBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__emptyBlockFunction_block_impl_0)};
>
> //block体内的逻辑代码被编译到了此函数中
> static void __BlockStructureViewController__emptyBlockFunction_block_func_0(struct __BlockStructureViewController__emptyBlockFunction_block_impl_0 *__cself) 
> {
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_dd128d_mi_0);
> }
> ```
>
> **分析调用步骤**
>
> 1、 调用\_I\_BlockStructureViewController\_emptyBlockFunction方法；
>
> ```
> //emptyBlockFunction编译后方法名为_I_BlockStructureViewController_emptyBlockFunction。此方法注册到了当前类的方法列表_method_list_t中，
>
> static struct /*_method_list_t*/ {
>     ...
>     {(struct objc_selector *)"emptyBlockFunction", "v16@0:8", (void *)_I_BlockStructureViewController_emptyBlockFunction},
>     ...
> };
> ```
>
> 2、block体中逻辑代码编译后的函数\_\_BlockStructureViewController\_\_emptyBlockFunction\_block\_func\_0
>
> ```
> //block体内的逻辑代码被编译到了此函数中
> static void __BlockStructureViewController__emptyBlockFunction_block_func_0(struct __BlockStructureViewController__emptyBlockFunction_block_impl_0 *__cself) 
> {
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_dd128d_mi_0);
> }
> ```
>
> 3、初始化描述block结构体信息的\_\_BlockStructureViewController\_\_emptyBlockFunction\_block\_desc\_0\_DATA
>
> ```
> //参数reserved：0
> //参数Block_size：block的size，sizeof(struct __BlockStructureViewController__emptyBlockFunction_block_impl_0)
>
> __BlockStructureViewController__emptyBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__emptyBlockFunction_block_impl_0)};
> ```
>
> 4、调用block构造方法\_\_BlockStructureViewController\_\_emptyBlockFunction\_block\_impl\_0，得到block函数指针
>
> ```
> //参数*fp：步骤2中的函数指针，(void *)__BlockStructureViewController__emptyBlockFunction_block_func_0
> //参数*desc：步骤3中__BlockStructureViewController__emptyBlockFunction_block_desc_0_DATA结构体地址，&__BlockStructureViewController__emptyBlockFunction_block_desc_0_DATA
> //参数flags：0
> void (*emptyBlock)(void) = ((void (*)())&__BlockStructureViewController__emptyBlockFunction_block_impl_0((void *)__BlockStructureViewController__emptyBlockFunction_block_func_0, &__BlockStructureViewController__emptyBlockFunction_block_desc_0_DATA));
> ```
>
> 5、调用block结构体中的函数指针FuncPtr即fp。
>
> ```
> //在步骤4中fp为(void *)__BlockStructureViewController__emptyBlockFunction_block_func_0,即调用此方法。
> ((void (*)(__block_impl *))((__block_impl *)emptyBlock)->FuncPtr)((__block_impl *)emptyBlock);
> ```
>
> 6、block体中的逻辑代码的调用
>
> ```
> //调用NSLog方法
> static void __BlockStructureViewController__emptyBlockFunction_block_func_0(struct __BlockStructureViewController__emptyBlockFunction_block_impl_0 *__cself) 
> {
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_dd128d_mi_0);
> }
> ```



