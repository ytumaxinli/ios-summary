#### **使用 WeakSelf.属性 的block**

> #### OC**代码**
>
> ```
> @property(nonatomic, strong)UILabel *label;
> - (void)weakSelfClassDataBlockFunction {
>     
>     __weak typeof(self) wself = self;
>     
>     void (^classDataBlock)(void) = ^{        
>         NSLog(@"八点钟学院 %@", wself.label);
>     };
>     classDataBlock();
> }
> ```
>
> #### **C++代码**
>
> ```
> //编译后的weakSelfClassDataBlockFunction。传入block构造函数的为wself
> static void _I_BlockStructureViewController_weakSelfClassDataBlockFunction(BlockStructureViewController * self, SEL _cmd) 
> {
>     __attribute__((objc_ownership(weak))) typeof(self) wself = self;
>     void (*classDataBlock)(void) = ((void (*)())&__BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__weakSelfClassDataBlockFunction_block_func_0, &__BlockStructureViewController__weakSelfClassDataBlockFunction_block_desc_0_DATA, wself, 570425344));
>
>     ((void (*)(__block_impl *))((__block_impl *)classDataBlock)->FuncPtr)((__block_impl *)classDataBlock);
> }
>
> //编译后的classDataBlock。新增的成员为__weak修饰，__weak wself。
> struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0 
> {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_desc_0* Desc;
>   BlockStructureViewController *const __weak wself;
>   __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_desc_0 *desc, BlockStructureViewController *const __weak _wself, int flags=0) : wself(_wself) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
> //block描述结构体
> static struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_desc_0 {
>   size_t reserved;
>   size_t Block_size;
>   void (*copy)(struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0*, struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0*);
>   void (*dispose)(struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0*);
> } __BlockStructureViewController__weakSelfClassDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0), __BlockStructureViewController__weakSelfClassDataBlockFunction_block_copy_0, __BlockStructureViewController__weakSelfClassDataBlockFunction_block_dispose_0};
>
>
> //block体内的逻辑代码编译生成的函数
> static void __BlockStructureViewController__weakSelfClassDataBlockFunction_block_func_0(struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0 *__cself) 
> {
>   BlockStructureViewController *const __weak wself = __cself->wself; // bound by copy
>   
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_9bab5e_mi_6, ((UILabel *(*)(id, SEL))(void *)objc_msgSend)((id)wself, sel_registerName("label")));
> }
>
> //_block_copy
> static void __BlockStructureViewController__weakSelfClassDataBlockFunction_block_copy_0(struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0*dst, struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_assign((void*)&dst->wself, (void*)src->wself, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
>
> //_block_dispose
> static void __BlockStructureViewController__weakSelfClassDataBlockFunction_block_dispose_0(struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_dispose((void*)src->wself, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
> ```
>
> **逻辑分析**
>
> 使用WeakSelf.属性的block，在编译后的block结构体的增加的成员BlockStructureViewController \*const \_\_weak wself;，即**对当前类进行了强引用，因此会造成循环引用**



