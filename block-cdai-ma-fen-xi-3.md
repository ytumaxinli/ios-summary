#### **使用对象类型局部外部变量的block**

> **OC源代码**
>
> ```
> - (void)objcDataBlockFunction {
>     
>     NSObject *objcData = [[NSObject alloc] init];
>     
>     void (^objcDataBlock)(void) = ^{
>         NSLog(@"对象类型局部变量, %@", objcData);
>     };
>     objcData = nil;
>     objcDataBlock();
>     NSLog(@"对象类型局部变量_1, %@", objcData);
> }
>
> //运行结果
> [14994:871902] 对象类型局部变量, <NSObject: 0x2833574d0>
> [14994:871902] 对象类型局部变量_1, (null)
> ```
>
> **C++ 代码**
>
> ```
> //编译后的objcDataBlockFunction方法
>   Flags = 570425344
> static void _I_BlockStructureViewController_objcDataBlockFunction(BlockStructureViewController * self, SEL _cmd) {
>
>     NSObject *objcData = ((NSObject *(*)(id, SEL))(void *)objc_msgSend)((id)((NSObject *(*)(id, SEL))(void *)objc_msgSend)((id)objc_getClass("NSObject"), sel_registerName("alloc")), sel_registerName("init"));
>
>     void (*objcDataBlock)(void) = ((void (*)())&__BlockStructureViewController__objcDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__objcDataBlockFunction_block_func_0, &__BlockStructureViewController__objcDataBlockFunction_block_desc_0_DATA, objcData, 570425344));
>     objcData = __null;
>     ((void (*)(__block_impl *))((__block_impl *)objcDataBlock)->FuncPtr)((__block_impl *)objcDataBlock);
>     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_10, objcData);
> }
>
> //编译后的objcDataBlock，新增成员NSObject *__strong objcData;
> struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0 {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__objcDataBlockFunction_block_desc_0* Desc;
>   NSObject *__strong objcData;
>   __BlockStructureViewController__objcDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__objcDataBlockFunction_block_desc_0 *desc, NSObject *__strong _objcData, int flags=0) : objcData(_objcData) {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
>
> //block描述结构体，这里与之前相比较多了两个函数指针，copy、dispose这两个方法在block的拷贝和释放时使用
> static struct __BlockStructureViewController__objcDataBlockFunction_block_desc_0 
> {
>   size_t reserved;
>   size_t Block_size;
>   void (*copy)(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*, struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*);
>   void (*dispose)(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*);
> } __BlockStructureViewController__objcDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0), __BlockStructureViewController__objcDataBlockFunction_block_copy_0, __BlockStructureViewController__objcDataBlockFunction_block_dispose_0};
>
>
> //block体内的逻辑代码编译生成的函数
> static void __BlockStructureViewController__objcDataBlockFunction_block_func_0(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0 *__cself) 
> {
>   NSObject *__strong objcData = __cself->objcData; // bound by copy
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_9, objcData);
> }
>
> //block结构体中增加的外部变量索引 copy方法
> static void __BlockStructureViewController__objcDataBlockFunction_block_copy_0(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*dst, struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_assign((void*)&dst->objcData, (void*)src->objcData, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
>
> //block结构体中增加的外部变量索引 dispose方法
> static void __BlockStructureViewController__objcDataBlockFunction_block_dispose_0(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_dispose((void*)src->objcData, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
> ```
>
> **分析调用步骤**
>
> 1、当使用对象类型局部变量时，block编译后得到的结构体中新增成员NSObject \*\_\_strong objcData;。
>
> 2、block结构体构造方法调用时，将局部变量的对象传递给结构体成员objcData进行强引用。
>
> 3、block的回调方法通过参数block结构体来获取到结构体成员objcData对象。
>
> 4、局部变量和block成员objcData共同引用同一块地址，block成员强引用使对象引用计数+1。
>
> 5、block回调函数内不能修改，外部变量的值。
>
> 6、构造方法中flags=570425344,以及新增的copy和dispose方法在后面探讨



