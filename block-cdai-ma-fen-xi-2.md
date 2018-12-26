**简单类型（如int型）局部外部变量block**

> **OC源码**
>
> ```
> - (void)simpleDataBlockFunction {
>     
>     int i = 10;
>     
>     void (^simpleDataBlock)(void) = ^{        
>         NSLog(@"八点钟学院 %d", i);
>     };
>     
>     NSLog(@"i == %d", i);
>     
>     simpleDataBlock();
> }
> ```
>
> **C++ 代码**
>
> ```
> //编译后的simpleDataBlockFunction方法
> static void _I_BlockStructureViewController_simpleDataBlockFunction(BlockStructureViewController * self, SEL _cmd) 
> {
>   int i = 10;
>
>   void (*simpleDataBlock)(void) = ((void (*)())&__BlockStructureViewController__simpleDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__simpleDataBlockFunction_block_func_0, &__BlockStructureViewController__simpleDataBlockFunction_block_desc_0_DATA, i));
>
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_dd128d_mi_2, i);
>
>   ((void (*)(__block_impl *))((__block_impl *)simpleDataBlock)->FuncPtr)((__block_impl *)simpleDataBlock);
> }
>
> //block实现结构体
> struct __block_impl {
>   void *isa;
>   int Flags;      //与Block源码中Block_private.h中的枚举相对应。标示block是否被copy、是否为全局block、是否为堆block等
>   int Reserved;
>   void *FuncPtr;  //函数调用指针。编译后block中的逻辑代码被编译到了一个独立函数中，此函数指针用来指向和调用这个独立函数。
> };
>
>
> //block描述结构体
> static struct __BlockStructureViewController__simpleDataBlockFunction_block_desc_0 {
>   size_t reserved;
>   size_t Block_size;
> } __BlockStructureViewController__simpleDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__simpleDataBlockFunction_block_impl_0)};
>
>
> //编译后的simpleDataBlock结构体
> struct __BlockStructureViewController__simpleDataBlockFunction_block_impl_0 
> {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__simpleDataBlockFunction_block_desc_0* Desc;
>   int i;
>   __BlockStructureViewController__simpleDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__simpleDataBlockFunction_block_desc_0 *desc, int _i, int flags=0) : i(_i) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
> //block体内的逻辑代码编译生成的函数
> static void __BlockStructureViewController__simpleDataBlockFunction_block_func_0(struct __BlockStructureViewController__simpleDataBlockFunction_block_impl_0 *__cself) 
> {
>   int i = __cself->i; // bound by copy  
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_dd128d_mi_1, i);
> }
> ```
>
> **分析调用步骤**
>
> 1、调用\_I\_BlockStructureViewController\_simpleDataBlockFunction方法；
>
> 2、block体中逻辑代码编译后的函数\_\_BlockStructureViewController\_\_simpleDataBlockFunction\_block\_func\_0
>
> 3、初始化描述block结构体信息的\_\_BlockStructureViewController\_\_simpleDataBlockFunction\_block\_desc\_0\_DATA
>
> 4、调用block构造方法\_\_BlockStructureViewController\_\_emptyBlockFunction\_block\_impl\_0，得到block函数指针
>
> ```
> //将局部变量i通过构造方法传给block结构体成员变量i
> void (*simpleDataBlock)(void) = ((void (*)())&__BlockStructureViewController__simpleDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__simpleDataBlockFunction_block_func_0, &__BlockStructureViewController__simpleDataBlockFunction_block_desc_0_DATA, i));
>
> //编译后的simpleDataBlock结构体，多了一个成员变量i
> struct __BlockStructureViewController__simpleDataBlockFunction_block_impl_0 
> {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__simpleDataBlockFunction_block_desc_0* Desc;
>   int i;
>   __BlockStructureViewController__simpleDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__simpleDataBlockFunction_block_desc_0 *desc, int _i, int flags=0) : i(_i) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
> ```
>
> 5、调用block结构体中的函数指针FuncPtr即fp。
>
> 6、block体中的逻辑代码的调用
>
> ```
> static void __BlockStructureViewController__simpleDataBlockFunction_block_func_0(struct __BlockStructureViewController__simpleDataBlockFunction_block_impl_0 *__cself) 
> {
>   //从block结构体中取出成员i,进行打印。此 i 值在block构造方法中传入
>   int i = __cself->i; // bound by copy  
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_dd128d_mi_1, i);
> }
> ```



