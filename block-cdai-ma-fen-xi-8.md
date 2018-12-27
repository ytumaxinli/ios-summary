#### **使用外部全局变量的block**

> **OC源代码**
>
> ```
> int globalValueOne = 10;
>
> - (void)globalDataBlockFunction {
>     
>     void (^globalDataBlock)(void) = ^{
>         
>         NSLog(@"八点钟学院 %d", globalValueOne);
>     };
>     
>     globalDataBlock();
> }
> ```
>
> **C++代码**
>
> ```
> //编译后的globalValueOne变量，仍为全局变量
> int globalValueOne = 10;
>
> //编译后的globalDataBlockFunction方法
> static void _I_BlockStructureViewController_globalDataBlockFunction(BlockStructureViewController * self, SEL _cmd) {
>
>     void (*globalDataBlock)(void) = ((void (*)())&__BlockStructureViewController__globalDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__globalDataBlockFunction_block_func_0, &__BlockStructureViewController__globalDataBlockFunction_block_desc_0_DATA));
>
>     ((void (*)(__block_impl *))((__block_impl *)globalDataBlock)->FuncPtr)((__block_impl *)globalDataBlock);
> }
>
> //编译后的globalDataBlock结构
> struct __BlockStructureViewController__globalDataBlockFunction_block_impl_0 
> {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__globalDataBlockFunction_block_desc_0* Desc;
>   __BlockStructureViewController__globalDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__globalDataBlockFunction_block_desc_0 *desc, int flags=0) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
> //block体中的逻辑代码
> static void __BlockStructureViewController__globalDataBlockFunction_block_func_0(struct __BlockStructureViewController__globalDataBlockFunction_block_impl_0 *__cself) 
> {
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_9bab5e_mi_9, globalValueOne);
> }
>
> //block的描述结构体
> static struct __BlockStructureViewController__globalDataBlockFunction_block_desc_0 {
>   size_t reserved;
>   size_t Block_size;
> } __BlockStructureViewController__globalDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__globalDataBlockFunction_block_impl_0)};
> ```
>
> **逻辑分析**
>
> 1.当使用外部全局变量时，block编译后得到的结构体中没有新增成员，即block体中使用的外部变量没有新增到block结构体中。
>
> 2.block体中的逻辑代码，编译后生成的函数直接使用此外部全局变量。



