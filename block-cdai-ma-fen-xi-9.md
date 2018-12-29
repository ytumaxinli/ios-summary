#### 使用全局静态变量的Block

> **OC源代码**
>
> ```
> static int staticData = 0;
> - (void)staticDataBlockFunction {
>     
>     void (^staticDataBlock)(void) = ^{
>         staticData = 1;
>         NSLog(@"静态变量 %d", staticData);
>     };
>     staticData = 2;
>     staticDataBlock();
>     NSLog(@"全局变量_2 %d", staticData);
> }
>
> //运行结果
> [14875:867218] 静态变量 1
> [14875:867218] 全局变量_2 1
> ```
>
> **C++代码**
>
> ```
> //与Block全部变量的情况类似
>
>
> static int staticData = 0;
>
> static void _I_BlockStructureViewController_staticDataBlockFunction(BlockStructureViewController * self, SEL _cmd) {
>
>     void (*staticDataBlock)(void) = ((void (*)())&__BlockStructureViewController__staticDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__staticDataBlockFunction_block_func_0, &__BlockStructureViewController__staticDataBlockFunction_block_desc_0_DATA));
>     staticData = 2;
>     ((void (*)(__block_impl *))((__block_impl *)staticDataBlock)->FuncPtr)((__block_impl *)staticDataBlock);
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_4, staticData);
> }
>
>
> struct __BlockStructureViewController__staticDataBlockFunction_block_impl_0 {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__staticDataBlockFunction_block_desc_0* Desc;
>   __BlockStructureViewController__staticDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__staticDataBlockFunction_block_desc_0 *desc, int flags=0) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
>
> };
> static void __BlockStructureViewController__staticDataBlockFunction_block_func_0(struct __BlockStructureViewController__staticDataBlockFunction_block_impl_0 *__cself) 
> {
>     staticData = 1;
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_3, staticData);
> }
>
> static struct __BlockStructureViewController__staticDataBlockFunction_block_desc_0 
> {
>   size_t reserved;
>   size_t Block_size;
> } __BlockStructureViewController__staticDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__staticDataBlockFunction_block_impl_0)};
> ```
>
> **逻辑分析**
>
> 1.当使用外部全局静态变量时，block编译后得到的结构体中**不新增成员**
>
> 2.block回调方法**直接使用**此外部全局静态变量，不通过block结构体传递。



