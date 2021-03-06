#### **使用\_\_block外部局部变量的block**

> **OC源码**
>
> ```
> - (void)blockDataBlockFunction {
>     
>     __block int a = 0;   ///栈区
>     
>     void (^blockDataBlock)(void) = ^{
>         a = 1;
>         NSLog(@"__block变量_1, %d", a);
>     };
>     a = 2;
>     blockDataBlock();
>     NSLog(@"__block变量_2, %d", a);
> }
>
> //打印结果
> [1181:69230] __block变量_1, 1
> [1181:69230] __block变量_2, 1
> ```
>
> **C++代码**
>
> ```
> //__block编译后变为了此结构体
> struct __Block_byref_a_0 
> {
>   void *__isa;
> __Block_byref_a_0 *__forwarding;
>  int __flags;
>  int __size;
>  int a;
> };
>
>
> //编译后的blockDataBlockFunction方法。block构造方法传入的是__Block_byref_a_0结构体的地址(__Block_byref_a_0 *)&a
> //__Block_byref_a_0结构体的*__forwarding=&a
> static void _I_BlockStructureViewController_blockDataBlockFunction(BlockStructureViewController * self, SEL _cmd) 
> {
>     __attribute__((__blocks__(byref))) __Block_byref_a_0 a = {(void*)0,(__Block_byref_a_0 *)&a, 0, sizeof(__Block_byref_a_0), 0};
>
>     void (*blockDataBlock)(void) = ((void (*)())&__BlockStructureViewController__blockDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__blockDataBlockFunction_block_func_0, &__BlockStructureViewController__blockDataBlockFunction_block_desc_0_DATA, (__Block_byref_a_0 *)&a, 570425344));
>     (a.__forwarding->a) = 2;
>     ((void (*)(__block_impl *))((__block_impl *)blockDataBlock)->FuncPtr)((__block_impl *)blockDataBlock);
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_18, (a.__forwarding->a));
> }
>
> //编译后的blockDataBlock实现。增加了一个__Block_byref_a_0结构体指针a.
> struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0 
> {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__blockDataBlockFunction_block_desc_0* Desc;
>   __Block_byref_a_0 *a; // by ref
>   __BlockStructureViewController__blockDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__blockDataBlockFunction_block_desc_0 *desc, __Block_byref_a_0 *_a, int flags=0) : a(_a->__forwarding) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
> //block回调方法
> static void __BlockStructureViewController__blockDataBlockFunction_block_func_0(struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0 *__cself) 
> {
>   __Block_byref_a_0 *a = __cself->a; // bound by ref
>
>   (a->__forwarding->a) = 1;
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_17, (a->__forwarding->a));
> }
>
> //_block_copy
> static void __BlockStructureViewController__blockDataBlockFunction_block_copy_0(struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0*dst, struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_assign((void*)&dst->a, (void*)src->a, 8/*BLOCK_FIELD_IS_BYREF*/);
> }
>
> //_block_dispose
> static void __BlockStructureViewController__blockDataBlockFunction_block_dispose_0(struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_dispose((void*)src->a, 8/*BLOCK_FIELD_IS_BYREF*/);
> }
>
> //block描述结构体
> static struct __BlockStructureViewController__blockDataBlockFunction_block_desc_0 {
>   size_t reserved;
>   size_t Block_size;
>   void (*copy)(struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0*, struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0*);
>   void (*dispose)(struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0*);
> } __BlockStructureViewController__blockDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0), __BlockStructureViewController__blockDataBlockFunction_block_copy_0, __BlockStructureViewController__blockDataBlockFunction_block_dispose_0};
> ```
>
> **逻辑分析**
>
> 1.调用 \_I\_BlockStructureViewController\_blockDataBlockFunction方法；
>
> 2.初始化\_\_block int a = 100;
>
> > 2.1\_\_block编译为了\_\_Block\_byref\_a\_0结构体
> >
> > ```
> > struct __Block_byref_a_0 
> > {
> >   void *__isa;
> > __Block_byref_a_0 *__forwarding;
> >  int __flags;
> >  int __size;
> >  int a;
> > };
> > ```
> >
> > 2.2初始化结构体，\_\_Block\_byref\_a\_0 \*\_\_forwarding=&a；
> >
> > ```
> > __attribute__((__blocks__(byref))) __Block_byref_a_0 a = {(void*)0,(__Block_byref_a_0 *)&a, 0, sizeof(__Block_byref_a_0), 100};
> > ```
>
> 3、block体中逻辑代码编译后的函数\_\_BlockStructureViewController\_\_blockDataBlockFunction\_block\_func\_0
>
> 4、描述block结构体信息的\_\_BlockStructureViewController\_\_blockDataBlockFunction\_block\_desc\_0\_DATA
>
> 5、调用block构造方法\_\_BlockStructureViewController\_\_blockDataBlockFunction\_block\_impl\_0，得到block函数指针。
>
> > 5.1 构造方法参数: \_\_Block\_byref\_a\_0 \*a=&a, flags= 570425344
> >
> > ```
> > void (*blockDataBlock)(void) = ((void (*)())&__BlockStructureViewController__blockDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__blockDataBlockFunction_block_func_0, &__BlockStructureViewController__blockDataBlockFunction_block_desc_0_DATA, (__Block_byref_a_0 *)&a, 570425344));
> > ```
> >
> > 5.2 block实现结构体中，增加了成员\_\_Block\_byref\_a\_0 \*a
> >
> > ```
> > struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0 
> > {
> >   struct __block_impl impl;
> >   struct __BlockStructureViewController__blockDataBlockFunction_block_desc_0* Desc;
> >   __Block_byref_a_0 *a; // by ref
> >   __BlockStructureViewController__blockDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__blockDataBlockFunction_block_desc_0 *desc, __Block_byref_a_0 *_a, int flags=0) : a(_a->__forwarding) {
> >     impl.isa = &_NSConcreteStackBlock;
> >     impl.Flags = flags;
> >     impl.FuncPtr = fp;
> >     Desc = desc;
> >   }
> > };
> > ```
>
> 6.block体中的逻辑代码的调用
>
> ```
> static void __BlockStructureViewController__blockDataBlockFunction_block_func_0(struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0 *__cself) 
> {
>   __Block_byref_a_0 *a = __cself->a; // bound by ref
>   
>   (a->__forwarding->a) = 1000;
>   
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_9bab5e_mi_7, (a->__forwarding->a));
> }
> ```



