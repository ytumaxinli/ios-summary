#### 使用局部静态变量的Block

> **OC源代码**
>
> ```
> typedef void(^eocblock)(void);
> @interface BlockStructureViewController () {
>     eocblock instanceBlock;
> }
>
> - (void)tmpStaticDataBlockFunction {
>     
>     static int b = 11;
>     
>     instanceBlock = ^{
>         
> //        b = 110;
>         NSLog(@"八点钟学院 %d", b);
>         
>     };
>     
>     b= 100;
>     instanceBlock();
> }
> ```
>
> **C++代码**
>
> ```
> //instanceBlock成员变量，增加到了当前类的结构体成员中，并使用__strong修饰
> struct BlockStructureViewController_IMPL {
>   .....
>   __strong eocblock instanceBlock;
>   .....
> };
>
> //编译后的tmpStaticDataBlockFunction方法
> static void _I_BlockStructureViewController_tmpStaticDataBlockFunction(BlockStructureViewController * self, SEL _cmd) {
>     //静态变量
>     static int b = 11;
>     //将静态变量b的地址&b，传入block结构的构造函数中
>     (*(__strong eocblock *)((char *)self + OBJC_IVAR_$_BlockStructureViewController$instanceBlock)) = ((void (*)())&__BlockStructureViewController__tmpStaticDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__tmpStaticDataBlockFunction_block_func_0, &__BlockStructureViewController__tmpStaticDataBlockFunction_block_desc_0_DATA, &b));
>
>     b= 100;
>     
>     ((void (*)(__block_impl *))((__block_impl *)(*(__strong eocblock *)((char *)self + OBJC_IVAR_$_BlockStructureViewController$instanceBlock)))->FuncPtr)((__block_impl *)(*(__strong eocblock *)((char *)self + OBJC_IVAR_$_BlockStructureViewController$instanceBlock)));
> }
>
> //编译后的instanceBlock结构。新增成员指针b,int *b。
> struct __BlockStructureViewController__tmpStaticDataBlockFunction_block_impl_0 {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__tmpStaticDataBlockFunction_block_desc_0* Desc;
>   int *b;
>   __BlockStructureViewController__tmpStaticDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__tmpStaticDataBlockFunction_block_desc_0 *desc, int *_b, int flags=0) : b(_b) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
> //block体中逻辑代码编译到此函数中
> static void __BlockStructureViewController__tmpStaticDataBlockFunction_block_func_0(struct __BlockStructureViewController__tmpStaticDataBlockFunction_block_impl_0 *__cself) 
> {
>   //获取指针b
>   int *b = __cself->b; // bound by copy
>
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_9bab5e_mi_11, (*b));
> }
>
> //block描述结构体
> static struct __BlockStructureViewController__tmpStaticDataBlockFunction_block_desc_0 {
>   size_t reserved;
>   size_t Block_size;
> } __BlockStructureViewController__tmpStaticDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__tmpStaticDataBlockFunction_block_impl_0)};
> ```
>
> **逻辑分析**
>
> 1.当使用局部静态变量时，block编译后得到的结构体中**新增成员指针b\(int \*b\)，在block构造方法中传递的是b的地址\(&b\).**
>
> 2.block体中的逻辑代码，编译后生成的函数**使用局部静态变量的指针**。



