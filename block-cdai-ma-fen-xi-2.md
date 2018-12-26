**简单类型（如int型）外部变量block**

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
>
>
> //block描述结构体
> static struct __BlockStructureViewController__simpleDataBlockFunction_block_desc_0 {
>   size_t reserved;
>   size_t Block_size;
> } __BlockStructureViewController__simpleDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__simpleDataBlockFunction_block_impl_0)};
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
> //编译后的block体逻辑代码
> static void __BlockStructureViewController__simpleDataBlockFunction_block_func_0(struct __BlockStructureViewController__simpleDataBlockFunction_block_impl_0 *__cself) 
> {
>   int i = __cself->i; // bound by copy  
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_dd128d_mi_1, i);
> }
>
>
> ```



