#### 使用全局静态变量的Block

> **OC源代码**
>
> ```
> - (void)staticDataBlockFunction {
>     
>     void (^staticDataBlock)(void) = ^{
>         
>         NSLog(@"八点钟学院 %d", staticValue);
>     };
>     staticDataBlock();
> }
> ```
>
> **C++代码**
>
> ```
> //编译后的staticValue，仍为全局静态变量
> static int staticValue = 5;
>
> //编译后的staticDataBlockFunction方法
> static void _I_BlockStructureViewController_staticDataBlockFunction(BlockStructureViewController * self, SEL _cmd) {
>
>     void (*staticDataBlock)(void) = ((void (*)())&__BlockStructureViewController__staticDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__staticDataBlockFunction_block_func_0, &__BlockStructureViewController__staticDataBlockFunction_block_desc_0_DATA));
>
>     ((void (*)(__block_impl *))((__block_impl *)staticDataBlock)->FuncPtr)((__block_impl *)staticDataBlock);
> }
>
> //编译后的staticDataBlock结构
> struct __BlockStructureViewController__staticDataBlockFunction_block_impl_0 
> {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__staticDataBlockFunction_block_desc_0* Desc;
>   __BlockStructureViewController__staticDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__staticDataBlockFunction_block_desc_0 *desc, int flags=0) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
> //block体内的逻辑代码编译到此函数中
> static void __BlockStructureViewController__staticDataBlockFunction_block_func_0(struct __BlockStructureViewController__staticDataBlockFunction_block_impl_0 *__cself) 
> {
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_9bab5e_mi_10, staticValue);
> }
>
> //block描述结构体
> static struct __BlockStructureViewController__staticDataBlockFunction_block_desc_0 {
>   size_t reserved;
>   size_t Block_size;
> } __BlockStructureViewController__staticDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__staticDataBlockFunction_block_impl_0)};
> ```
>
> **逻辑分析**
>
> 1.当使用外部全局静态变量时，block编译后得到的结构体中**没有新增成员**，即block体中使用的外部全局静态变量没有新增到block结构体中。
>
> 2.block体中的逻辑代码，编译后生成的函数**直接使用**此外部全局静态变量。



