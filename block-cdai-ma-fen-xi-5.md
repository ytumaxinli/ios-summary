**使用成员变量的block**

> OC源码
>
> ```
> @interface BlockStructureViewController () {
>     UILabel *label;
> }
>
> - (void)classDataBlockFunction {
>     
>     void (^classDataBlock)(void) = ^{
>         
>         NSLog(@"八点钟学院 %@", label);
>     };
>     
>     classDataBlock();
> }
> ```
>
> C++代码
>
> ```
> //编译后的classDataBlockFunction
> static void _I_BlockStructureViewController_classDataBlockFunction(BlockStructureViewController * self, SEL _cmd) 
> {
>     void (*classDataBlock)(void) = ((void (*)())&__BlockStructureViewController__classDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__classDataBlockFunction_block_func_0, &__BlockStructureViewController__classDataBlockFunction_block_desc_0_DATA, self, 570425344));
>
>     ((void (*)(__block_impl *))((__block_impl *)classDataBlock)->FuncPtr)((__block_impl *)classDataBlock);
> }
>
>
> //编译后的classDataBlock。增加了__strong self强引用
> struct __BlockStructureViewController__classDataBlockFunction_block_impl_0 {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__classDataBlockFunction_block_desc_0* Desc;
>   BlockStructureViewController *const __strong self;
>   __BlockStructureViewController__classDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__classDataBlockFunction_block_desc_0 *desc, BlockStructureViewController *const __strong _self, int flags=0) : self(_self) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
>
> //block体内的逻辑代码编译生成的函数
> static void __BlockStructureViewController__classDataBlockFunction_block_func_0(struct __BlockStructureViewController__classDataBlockFunction_block_impl_0 *__cself) 
> {
>   BlockStructureViewController *const __strong self = __cself->self; // bound by copy
>   
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_9bab5e_mi_5, (*(UILabel *__strong *)((char *)self + OBJC_IVAR_$_BlockStructureViewController$_label)));
> }
>     
> //_block_copy
> static void __BlockStructureViewController__classDataBlockFunction_block_copy_0(struct __BlockStructureViewController__classDataBlockFunction_block_impl_0*dst, struct __BlockStructureViewController__classDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_assign((void*)&dst->self, 
>   (void*)src->self, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
>
>
> //_block_dispose
> static void __BlockStructureViewController__classDataBlockFunction_block_dispose_0(struct __BlockStructureViewController__classDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_dispose((void*)src->self, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
>
> //block描述结构体
> static struct __BlockStructureViewController__classDataBlockFunction_block_desc_0 
> {
>   size_t reserved;
>   size_t Block_size;
>   void (*copy)(struct __BlockStructureViewController__classDataBlockFunction_block_impl_0*, struct __BlockStructureViewController__classDataBlockFunction_block_impl_0*);
>   void (*dispose)(struct __BlockStructureViewController__classDataBlockFunction_block_impl_0*);
> } __BlockStructureViewController__classDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__classDataBlockFunction_block_impl_0), __BlockStructureViewController__classDataBlockFunction_block_copy_0, __BlockStructureViewController__classDataBlockFunction_block_dispose_0};
> ```
>
> **逻辑分析**
>
> 使用成员变量的block，在编译后的block结构体的增加的成员BlockStructureViewController \*const \_\_strong self;，即**对当前类进行了强引用，因此如果此block也被当前类强引用时就会造成循环引用。**



