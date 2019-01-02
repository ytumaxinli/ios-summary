#### **使用 weakSelf.属性 的block**

> #### OC**代码**
>
> ```
> @property(nonatomic, strong)UILabel *label;
> - (void)weakSelfClassDataBlockFunction {
>     
>     __weak typeof(self) wself = self;
>     void (^classDataBlock)(void) = ^{
>         NSLog(@"weakself成员变量 %@", wself.label);
>     };
>     _label = nil;
>     classDataBlock();
>     NSLog(@"weakself成员变量_1 %@", wself.label);
> }
>
> //运行结果
> [1149:67840] weakself成员变量 (null)
> [1149:67840] weakself成员变量_1 (null)
> ```
>
> #### **C++代码**
>
> ```
> //编译后的weakSelfClassDataBlockFunction。传入block构造函数的为wself
> static void _I_BlockStructureViewController_weakSelfClassDataBlockFunction(BlockStructureViewController * self, SEL _cmd) {
>
>     __attribute__((objc_ownership(weak))) typeof(self) wself = self;
>     void (*classDataBlock)(void) = ((void (*)())&__BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__weakSelfClassDataBlockFunction_block_func_0, &__BlockStructureViewController__weakSelfClassDataBlockFunction_block_desc_0_DATA, wself, 570425344));
>     (*(UILabel *__strong *)((char *)self + OBJC_IVAR_$_BlockStructureViewController$_label)) = __null;
>     ((void (*)(__block_impl *))((__block_impl *)classDataBlock)->FuncPtr)((__block_impl *)classDataBlock);
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_16, ((UILabel *(*)(id, SEL))(void *)objc_msgSend)((id)wself, sel_registerName("label")));
> }
>
> //block结构体，新增成员变量BlockStructureViewController *const __weak wself;
> struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0 {
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
> static void __BlockStructureViewController__weakSelfClassDataBlockFunction_block_func_0(struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0 *__cself) 
> {
>     BlockStructureViewController *const __weak wself = __cself->wself; // bound by copy
>
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_15, ((UILabel *(*)(id, SEL))(void *)objc_msgSend)((id)wself, sel_registerName("label")));
> }
>     
> static void __BlockStructureViewController__weakSelfClassDataBlockFunction_block_copy_0(struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0*dst, struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0*src)
> {
>   _Block_object_assign((void*)&dst->wself, (void*)src->wself, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
>
> static void __BlockStructureViewController__weakSelfClassDataBlockFunction_block_dispose_0(struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_dispose((void*)src->wself, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
>
> static struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_desc_0 {
>   size_t reserved;
>   size_t Block_size;
>   void (*copy)(struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0*, struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0*);
>   void (*dispose)(struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0*);
> } __BlockStructureViewController__weakSelfClassDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__weakSelfClassDataBlockFunction_block_impl_0), __BlockStructureViewController__weakSelfClassDataBlockFunction_block_copy_0, __BlockStructureViewController__weakSelfClassDataBlockFunction_block_dispose_0};
> ```
>
> **逻辑分析**
>
> 使用weakSelf.属性的block，在编译后的block结构体的增加的成员BlockStructureViewController \*const \_\_weak wself;，即**对当前类进行了弱引用，不会造成循环引用**



