#### **使用\_\_weak类型局部变量的block**

> #### **OC源代码**
>
> ```
> - (void)weakObjcDataBlockFunction {
>     
>    __weak NSObject *weakObjcData = [[NSObject alloc] init];
>     
>     void (^objcDataBlock)(void) = ^{
>         NSLog(@"weak对象类型局部变量, %@", weakObjcData);
>     };
>     weakObjcData = nil;
>     objcDataBlock();
>     NSLog(@"weak对象类型局部变量_1, %@", weakObjcData);
> }
>
> //运行结果
> [15148:878597] weak对象类型局部变量, (null)
> [15148:878597] weak对象类型局部变量_1, (null)
> ```
>
> **C++代码**
>
> ```
> static void _I_BlockStructureViewController_weakObjcDataBlockFunction(BlockStructureViewController * self, SEL _cmd) {
>
>    __attribute__((objc_ownership(weak))) NSObject *weakObjcData = ((NSObject *(*)(id, SEL))(void *)objc_msgSend)((id)((NSObject *(*)(id, SEL))(void *)objc_msgSend)((id)objc_getClass("NSObject"), sel_registerName("alloc")), sel_registerName("init"));
>
>     void (*objcDataBlock)(void) = ((void (*)())&__BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__weakObjcDataBlockFunction_block_func_0, &__BlockStructureViewController__weakObjcDataBlockFunction_block_desc_0_DATA, weakObjcData, 570425344));
>     weakObjcData = __null;
>     ((void (*)(__block_impl *))((__block_impl *)objcDataBlock)->FuncPtr)((__block_impl *)objcDataBlock);
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_12, weakObjcData);
> }
>
>
> struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0 {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__weakObjcDataBlockFunction_block_desc_0* Desc;
>   NSObject *__weak weakObjcData;
>   __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__weakObjcDataBlockFunction_block_desc_0 *desc, NSObject *__weak _weakObjcData, int flags=0) : weakObjcData(_weakObjcData) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
> static void __BlockStructureViewController__weakObjcDataBlockFunction_block_func_0(struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0 *__cself) 
> ,{
>   NSObject *__weak weakObjcData = __cself->weakObjcData; // bound by copy
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_11, weakObjcData);
> }
>
> static void __BlockStructureViewController__weakObjcDataBlockFunction_block_copy_0(struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0*dst, struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_assign((void*)&dst->weakObjcData, (void*)src->weakObjcData, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
>
> static void __BlockStructureViewController__weakObjcDataBlockFunction_block_dispose_0(struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_dispose((void*)src->weakObjcData, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
>
> static struct __BlockStructureViewController__weakObjcDataBlockFunction_block_desc_0 {
>   size_t reserved;
>   size_t Block_size;
>   void (*copy)(struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0*, struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0*);
>   void (*dispose)(struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0*);
> } __BlockStructureViewController__weakObjcDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0), __BlockStructureViewController__weakObjcDataBlockFunction_block_copy_0, __BlockStructureViewController__weakObjcDataBlockFunction_block_dispose_0};
> ```
>
> **逻辑分析**
>
> 当使用**\_\_**weak修饰的局部外部变量时,在编译后的block结构体的增加的成员NSObject \*\_\_weak weakObjcData也使用了\_\_weak修饰，即对外部局部变量进行**弱引用**



