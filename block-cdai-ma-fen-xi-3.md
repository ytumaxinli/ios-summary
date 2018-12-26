**使用objc对象局部外部变量的block**

> **OC源代码**
>
> ```
> - (void)objcDataBlockFunction {
>     
>     UILabel *tmpLabel = [[UILabel alloc] init];
>     
>     void (^objcDataBlock)(void) = ^{        
>         NSLog(@"八点钟学院, %@", tmpLabel);
>     };
>     
>     objcDataBlock();
> }
> ```
>
> **C++ 代码**
>
> ```
> //编译后的objcDataBlockFunction方法
> static void _I_BlockStructureViewController_objcDataBlockFunction(BlockStructureViewController * self, SEL _cmd) 
> {
>     UILabel *tmpLabel = ((UILabel *(*)(id, SEL))(void *)objc_msgSend)((id)((UILabel *(*)(id, SEL))(void *)objc_msgSend)((id)objc_getClass("UILabel"), sel_registerName("alloc")), sel_registerName("init"));
>
>     void (*objcDataBlock)(void) = ((void (*)())&__BlockStructureViewController__objcDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__objcDataBlockFunction_block_func_0, &__BlockStructureViewController__objcDataBlockFunction_block_desc_0_DATA, tmpLabel, 570425344));
>
>     ((void (*)(__block_impl *))((__block_impl *)objcDataBlock)->FuncPtr)((__block_impl *)objcDataBlock);
> }
>
> //编译后的objcDataBlock
> struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0 
> {
>   struct __block_impl impl;
>   struct __BlockStructureViewController__objcDataBlockFunction_block_desc_0* Desc;
>   UILabel *__strong tmpLabel;
>   __BlockStructureViewController__objcDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__objcDataBlockFunction_block_desc_0 *desc, UILabel *__strong _tmpLabel, int flags=0) : tmpLabel(_tmpLabel) {
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
>   UILabel *__strong tmpLabel = __cself->tmpLabel; // bound by copy
>   
>   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_dd128d_mi_3, tmpLabel);
> }
>
> //block copy方法
> static void __BlockStructureViewController__objcDataBlockFunction_block_copy_0(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*dst, struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_assign((void*)&dst->tmpLabel, 
>   (void*)src->tmpLabel, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
>
> //block copy方法
> static void __BlockStructureViewController__objcDataBlockFunction_block_dispose_0(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*src) 
> {
>   _Block_object_dispose((void*)src->tmpLabel, 3/*BLOCK_FIELD_IS_OBJECT*/);
> }
> ```
>
> **分析调用步骤**
>
> 1、调用\_I\_BlockStructureViewController\_objcDataBlockFunction方法；
>
> 2、block体中逻辑代码编译后的函数\_\_BlockStructureViewController\_\_objcDataBlockFunction\_block\_func\_0
>
> 3、描述block结构体信息的\_\_BlockStructureViewController\_\_objcDataBlockFunction\_block\_desc\_0\_DATA
>
> ```
> //结构体与使用简单外部变量相比多了copy 和 dispose方法
> static struct __BlockStructureViewController__objcDataBlockFunction_block_desc_0 
> {
>   size_t reserved;
>   size_t Block_size;
>   void (*copy)(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*, struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*);
>   void (*dispose)(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*);
> } __BlockStructureViewController__objcDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0), __BlockStructureViewController__objcDataBlockFunction_block_copy_0, __BlockStructureViewController__objcDataBlockFunction_block_dispose_0};
> ```
>
> > 4、调用block构造方法\_\_BlockStructureViewController\_\_emptyBlockFunction\_block\_impl\_0，得到block函数指针
> >
> > ```
> > //将局部变量i通过构造方法传给block结构体成员变量i
> > void (*simpleDataBlock)(void) = ((void (*)())&__BlockStructureViewController__simpleDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__simpleDataBlockFunction_block_func_0, &__BlockStructureViewController__simpleDataBlockFunction_block_desc_0_DATA, i));
> >
> > //编译后的simpleDataBlock结构体，多了一个成员变量i
> > struct __BlockStructureViewController__simpleDataBlockFunction_block_impl_0 
> > {
> >   struct __block_impl impl;
> >   struct __BlockStructureViewController__simpleDataBlockFunction_block_desc_0* Desc;
> >   int i;
> >   __BlockStructureViewController__simpleDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__simpleDataBlockFunction_block_desc_0 *desc, int _i, int flags=0) : i(_i) {
> >     impl.isa = &_NSConcreteStackBlock;
> >     impl.Flags = flags;
> >     impl.FuncPtr = fp;
> >     Desc = desc;
> >   }
> > };
> > ```
> >
> > 5、调用block结构体中的函数指针FuncPtr即fp。
> >
> > 6、block体中的逻辑代码的调用
> >
> > ```
> >
> > ```



