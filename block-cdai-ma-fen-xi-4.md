**使用由\_\_weak修饰的局部外部变量的block**

> **OC源代码**
>
> ```
> - (void)weakObjcDataBlockFunction {
>     
>    __weak UILabel *tmpLabel = [[UILabel alloc] init];
>     
>     void (^objcDataBlock)(void) = ^{        
>         NSLog(@"八点钟学院, %@", tmpLabel);
>     };
>     
>     objcDataBlock();
> }
> ```
>
> **C++代码**
>
> ```
> //编译后的weakObjcDataBlockFunction方法
> static void _I_BlockStructureViewController_weakObjcDataBlockFunction(BlockStructureViewController * self, SEL _cmd) 
> {
>    __attribute__((objc_ownership(weak))) UILabel *tmpLabel = ((UILabel *(*)(id, SEL))(void *)objc_msgSend)((id)((UILabel *(*)(id, SEL))(void *)objc_msgSend)((id)objc_getClass("UILabel"), sel_registerName("alloc")), sel_registerName("init"));
>
>     void (*objcDataBlock)(void) = ((void (*)())&__BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__weakObjcDataBlockFunction_block_func_0, &__BlockStructureViewController__weakObjcDataBlockFunction_block_desc_0_DATA, tmpLabel, 570425344));
>
>     ((void (*)(__block_impl *))((__block_impl *)objcDataBlock)->FuncPtr)((__block_impl *)objcDataBlock);
> }
>
> //编译后的objcDataBlock
> struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0 
> {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__weakObjcDataBlockFunction_block_desc_0* Desc;
>   UILabel *__weak tmpLabel;
>   __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__weakObjcDataBlockFunction_block_desc_0 *desc, UILabel *__weak _tmpLabel, int flags=0) : tmpLabel(_tmpLabel) 
>   {
>     impl.isa = &_NSConcreteStackBlock;
>     impl.Flags = flags;
>     impl.FuncPtr = fp;
>     Desc = desc;
>   }
> };
>
> //block实现结构体
> struct __block_impl {
> void *isa;
> int Flags; //与Block源码中Block_private.h中的枚举相对应。标示block是否被copy、是否为全局block、是否为堆block等
> int Reserved;
> void *FuncPtr; //函数调用指针。编译后block中的逻辑代码被编译到了一个独立函数中，此函数指针用来指向和调用这个独立函数。
> };
>
> //block描述结构体
> static struct __BlockStructureViewController__weakObjcDataBlockFunction_block_desc_0 
> {
>   size_t reserved;
>   size_t Block_size;
>   void (*copy)(struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0*, struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0*);
>   void (*dispose)(struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0*);
> } __BlockStructureViewController__weakObjcDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0), __BlockStructureViewController__weakObjcDataBlockFunction_block_copy_0, __BlockStructureViewController__weakObjcDataBlockFunction_block_dispose_0};
>
> //block体内的逻辑代码编译生成的函数
> static void __BlockStructureViewController__weakObjcDataBlockFunction_block_func_0(struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0 *__cself) 
> {
>   UILabel *__weak tmpLabel = __cself->tmpLabel; // bound by copy
>   
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_dd128d_mi_4, tmpLabel);
> }
>
> //_block_copy
> static void __BlockStructureViewController__weakObjcDataBlockFunction_block_copy_0(struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0*dst, struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_assign((void*)&dst->tmpLabel, 
>   (void*)src->tmpLabel, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
>
> //_block_dispose
> static void __BlockStructureViewController__weakObjcDataBlockFunction_block_dispose_0(struct __BlockStructureViewController__weakObjcDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_dispose((void*)src->tmpLabel, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
> ```
>
> **逻辑分析**
>
> 使用\_\_weak修饰的block，在编译后的block结构体的增加的成员tmpLabel也使用了\_\_weak修饰，即对外部局部变量进行**弱引用**



