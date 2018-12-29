#### **使用基础类型（如int型）局部外部变量的block**

> #### **OC源码**
>
> ```
> - (void)simpleDataBlockFunction {
>     
>     int simpleData = 0;
>     void (^simpleDataBlock)(void) = ^{
>         NSLog(@"基础类型局部变量 == %d", simpleData);
>     };
>     simpleData = 1;
>     simpleDataBlock();
>     NSLog(@"基础类型局部变量 == %d", simpleData);
> }
>
> //运行结果
> [14948:869974] 基础类型局部变量 == 0
> [14948:869974] 基础类型局部变量 == 1
> ```
>
> **C++ 代码**
>
> ```
> //编译后的simpleDataBlockFunction方法
> static void _I_BlockStructureViewController_simpleDataBlockFunction(BlockStructureViewController * self, SEL _cmd) {
>
>     int simpleData = 0;
>     void (*simpleDataBlock)(void) = ((void (*)())&__BlockStructureViewController__simpleDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__simpleDataBlockFunction_block_func_0, &__BlockStructureViewController__simpleDataBlockFunction_block_desc_0_DATA, simpleData));
>     simpleData = 1;
>     ((void (*)(__block_impl *))((__block_impl *)simpleDataBlock)->FuncPtr)((__block_impl *)simpleDataBlock);
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_8, simpleData);
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
> struct __BlockStructureViewController__simpleDataBlockFunction_block_impl_0 {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__simpleDataBlockFunction_block_desc_0* Desc;
>   int simpleData;
>   __BlockStructureViewController__simpleDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__simpleDataBlockFunction_block_desc_0 *desc, int _simpleData, int flags=0) : simpleData(_simpleData) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
>
> //block回调方法
> static void __BlockStructureViewController__simpleDataBlockFunction_block_func_0(struct __BlockStructureViewController__simpleDataBlockFunction_block_impl_0 *__cself) 
> {
>   int simpleData = __cself->simpleData; // bound by copy
>
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_7, simpleData);
> }
> ```
>
> **逻辑分析**
>
> 1、当使用基础类型局部变量时，block编译后得到的结构体中新增成员int simpleData;。
>
> 2、block结构体构造方法调用时，将局部变量的值传递给结构体成员simpleData。
>
> 3、block的回调方法通过参数block结构体来获取到结构体成员simpleData的值。
>
> 4、在构造方法调用后，外部变量与结构体成员simpleData已经是不同的变量，因此改变外部变量不影响结构体成员的值。
>
> 5、block回调函数内不能修改，外部变量的值。



