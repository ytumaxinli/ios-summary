#### **使用外部全局变量的block**

> **OC源代码**
>
> ```
> int globalData = 0;
> - (void)globalDataBlockFunction {
>     
>     void (^globalDataBlock)(void) = ^{
>         globalData = 1;
>         NSLog(@"全局变量_1 %d", globalData);
>     };
>     globalData = 2;
>     globalDataBlock();
>     NSLog(@"全局变量_2 %d", globalData);
> }
>
> //运行结果
> [14840:865914] 全局变量_1 1
> [14840:865914] 全局变量_2 1
> ```
>
> **C++代码**
>
> ```
> //编译后的globalData变量，仍为全局变量
> int globalData = 0;
>
> //编译后的globalDataBlockFunction方法
> static void _I_BlockStructureViewController_globalDataBlockFunction(BlockStructureViewController * self, SEL _cmd) 
> {
>   void (*globalDataBlock)(void) = ((void (*)())&__BlockStructureViewController__globalDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__globalDataBlockFunction_block_func_0, &__BlockStructureViewController__globalDataBlockFunction_block_desc_0_DATA));
>   globalData = 2;
>   ((void (*)(__block_impl *))((__block_impl *)globalDataBlock)->FuncPtr)((__block_impl *)globalDataBlock);
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_2, globalData);
> }
>
> //编译后的globalDataBlock结构
> struct __BlockStructureViewController__globalDataBlockFunction_block_impl_0 {
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
> //回调方法
> static void __BlockStructureViewController__globalDataBlockFunction_block_func_0(struct __BlockStructureViewController__globalDataBlockFunction_block_impl_0 *__cself) 
> {
>     globalData = 1;
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_1, globalData);
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
> 1.当使用外部全局变量时，block编译后得到的结构体中不新增成员。
>
> 2.block的回调方法直接使用此外部全局变量，不通过block结构体索引。



