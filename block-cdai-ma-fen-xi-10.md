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
>     static int tmpStaticData = 0;
>     instanceBlock = ^{
>         tmpStaticData = 1;
>         NSLog(@"局部静态变量_1 %d", tmpStaticData);
>     };
>     
>     tmpStaticData = 2;
>     instanceBlock();
>     NSLog(@"局部静态变量_2 %d", tmpStaticData);
> }
>
> //运行结果
> [14893:867918] 局部静态变量_1 1
> [14893:867918] 局部静态变量_2 1
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
>     static int tmpStaticData = 0;
>     //将静态变量的地址传递到构造函数中
>     (*(__strong eocblock *)((char *)self + OBJC_IVAR_$_BlockStructureViewController$instanceBlock)) = ((void (*)())&__BlockStructureViewController__tmpStaticDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__tmpStaticDataBlockFunction_block_func_0, &__BlockStructureViewController__tmpStaticDataBlockFunction_block_desc_0_DATA, &tmpStaticData));
>
>     tmpStaticData = 2;
>     ((void (*)(__block_impl *))((__block_impl *)(*(__strong eocblock *)((char *)self + OBJC_IVAR_$_BlockStructureViewController$instanceBlock)))->FuncPtr)((__block_impl *)(*(__strong eocblock *)((char *)self + OBJC_IVAR_$_BlockStructureViewController$instanceBlock)));
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_6, tmpStaticData);
> }
>
> //编译后的instanceBlock结构。新增成员int *tmpStaticData。
> struct __BlockStructureViewController__tmpStaticDataBlockFunction_block_impl_0 {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__tmpStaticDataBlockFunction_block_desc_0* Desc;
>   int *tmpStaticData;
>   __BlockStructureViewController__tmpStaticDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__tmpStaticDataBlockFunction_block_desc_0 *desc, int *_tmpStaticData, int flags=0) : tmpStaticData(_tmpStaticData) {
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
>   //获取指针
>   int *tmpStaticData = __cself->tmpStaticData; // bound by copy
>   //通过指针赋值
>   (*tmpStaticData) = 1;
>   //通过指针取值
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_5, (*tmpStaticData));
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
> 1.当使用局部静态变量时，block编译后得到的结构体中**新增成员int \*tmpStaticData，在block构造方法中传递的是变量tmpStaticData的地址.**
>
> 2.block回调方法中**使用局部静态变量的指针，**该指针通过block结构体获取。



