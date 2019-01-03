#### OC源码

> ```
> - (void)blockDataBlockFunction {
>     
>     __block UILabel *a = [UILabel new];   ///栈区
>     
>     void (^blockDataBlock)(void) = ^{
>         NSLog(@"__block变量_1, %@", a);
>     };
>     
>     blockDataBlock();
>     a = nil;
>     blockDataBlock();
> }
>
> //运行结果
> [4705:275164] __block变量_1, <UILabel: 0x143d104d0; frame = (0 0; 0 0); userInteractionEnabled = NO; layer = <_UILabelLayer: 0x280ae8780>>
> [4705:275164] __block变量_1, (null)
> ```

#### **C++代码**

> **block相关**
>
> > blockDataBlockFunction方法
> >
> > ```
> > static void _I_BlockStructureViewController_blockDataBlockFunction(BlockStructureViewController * self, SEL _cmd) 
> > {
> >     __attribute__((__blocks__(byref))) __Block_byref_a_0 a = {(void*)0,(__Block_byref_a_0 *)&a, 33554432, sizeof(__Block_byref_a_0), __Block_byref_id_object_copy_131, __Block_byref_id_object_dispose_131, ((UILabel *(*)(id, SEL))(void *)objc_msgSend)((id)objc_getClass("UILabel"), sel_registerName("new"))};
> >
> >     //1. __BlockStructureViewController__blockDataBlockFunction_block_impl_0      见1
> >     //2. __BlockStructureViewController__blockDataBlockFunction_block_func_0      见2
> >     //3. __BlockStructureViewController__blockDataBlockFunction_block_desc_0_DATA 见3
> >     void (*blockDataBlock)(void) = ((void (*)())&__BlockStructureViewController__blockDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__blockDataBlockFunction_block_func_0, &__BlockStructureViewController__blockDataBlockFunction_block_desc_0_DATA, (__Block_byref_a_0 *)&a, 570425344));
> >
> >     ((void (*)(__block_impl *))((__block_impl *)blockDataBlock)->FuncPtr)((__block_impl *)blockDataBlock);
> >
> >     //修改堆内__block成员a
> >     (a.__forwarding->a) = __null;
> >     
> >     ((void (*)(__block_impl *))((__block_impl *)blockDataBlock)->FuncPtr)((__block_impl *)blockDataBlock);
> > }
> > ```
> >
> > 1. block结构体。新增成员\_\_Block\_byref\_a\_0 \*a指针
> >
> > ```
> > //1. block结构体。新增成员__Block_byref_a_0 *a指针
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
> >
> > 1. 回调方法
> >
> > ```
> > static void __BlockStructureViewController__blockDataBlockFunction_block_func_0(struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0 *__cself) 
> > {
> >   __Block_byref_a_0 *a = __cself->a; // bound by ref
> >
> >   (a->__forwarding->a) = 1;
> >   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_553b77_mi_18, (a->__forwarding->a));
> > }
> > ```
> >
> > 1. desc结构体 
> >
> > ```
> > static struct __BlockStructureViewController__blockDataBlockFunction_block_desc_0 
> > {
> >   size_t reserved;
> >   size_t Block_size;
> >   //见4.
> >   void (*copy)(struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0*, struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0*);
> >   void (*dispose)(struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0*);
> > } __BlockStructureViewController__blockDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0), __BlockStructureViewController__blockDataBlockFunction_block_copy_0, __BlockStructureViewController__blockDataBlockFunction_block_dispose_0};
> > ```
> >
> > 4.desc的copy实现。拷贝成员a
> >
> > ```
> > static void __BlockStructureViewController__blockDataBlockFunction_block_copy_0(struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0*dst, struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0*src) 
> > {
> >   //5.block源码_Block_object_assign方法调用
> >   _Block_object_assign((void*)&dst->a, (void*)src->a, 8/*BLOCK_FIELD_IS_BYREF*/);
> > }
> > ```
> >
> > 5.block源码\_Block\_object\_assign中调用\_Block\_byref\_copy
> >
> > ```
> > //5.block源码_Block_object_assign中调用_Block_byref_copy
> > void _Block_object_assign(void *destArg, const void *object, const int flags) {
> >     
> >     const void **dest = (const void **)destArg;
> >     switch ((flags & BLOCK_ALL_COPY_DISPOSE_FLAGS)) {
> >       ...    
> >       case BLOCK_FIELD_IS_BYREF | BLOCK_FIELD_IS_WEAK:
> >       case BLOCK_FIELD_IS_BYREF:
> >         //6.源码_Block_byref_copy
> >         *dest = _Block_byref_copy(object);
> >         break;
> >         ...
> >     }
> > }
> > ```
> >
> > 1. block源码\_\_block结构体拷贝
> >
> > ```
> > static struct Block_byref *_Block_byref_copy(const void *arg) {
> >     struct Block_byref *src = (struct Block_byref *)arg;
> >
> >     if ((src->forwarding->flags & BLOCK_REFCOUNT_MASK) == 0) {
> >         // 为堆block分配内存空间
> >         struct Block_byref *copy = (struct Block_byref *)malloc(src->size);
> >         copy->isa = NULL;
> >         
> >         // byref value 4 is logical refcount of 2: one for caller, one for stack
> >         copy->flags = src->flags | BLOCK_BYREF_NEEDS_FREE | 4;
> >         
> >         //将栈和堆的__block结构体的forwarding指针都指向堆__block内存
> >         copy->forwarding = copy; // patch heap copy to point to itself
> >         src->forwarding = copy;  // patch stack to point to heap copy
> >         copy->size = src->size;
> >
> >         if (src->flags & BLOCK_BYREF_HAS_COPY_DISPOSE) {
> >             // Trust copy helper to copy everything of interest
> >             // If more than one field shows up in a byref block this is wrong XXX
> >             struct Block_byref_2 *src2 = (struct Block_byref_2 *)(src+1);
> >             struct Block_byref_2 *copy2 = (struct Block_byref_2 *)(copy+1);
> >             copy2->byref_keep = src2->byref_keep;
> >             copy2->byref_destroy = src2->byref_destroy;
> >
> >             if (src->flags & BLOCK_BYREF_LAYOUT_EXTENDED) {
> >                 struct Block_byref_3 *src3 = (struct Block_byref_3 *)(src2+1);
> >                 struct Block_byref_3 *copy3 = (struct Block_byref_3*)(copy2+1);
> >                 copy3->layout = src3->layout;
> >             }
> >             //见7
> >             (*src2->byref_keep)(copy, src);
> >         }
> >         else {
> >             // Bitwise copy.
> >             // This copy includes Block_byref_3, if any.
> >             memmove(copy+1, src+1, src->size - sizeof(*src));
> >         }
> >     }
> >     // already copied to heap
> >     else if ((src->forwarding->flags & BLOCK_BYREF_NEEDS_FREE) == BLOCK_BYREF_NEEDS_FREE) {
> >         latching_incr_int(&src->forwarding->flags);
> >     }
> >     
> >     return src->forwarding;
> > }
> > ```
>
> **\_\_block局部变量相关**
>
> > ```
> > //__block的结构体。结构体新增强引用成员a，增加copy和dispose方法
> > struct __Block_byref_a_0 {
> >   void *__isa;
> >  __Block_byref_a_0 *__forwarding;
> >  int __flags;
> >  int __size;
> >  void (*__Block_byref_id_object_copy)(void*, void*); //拷贝成员UILabel *__strong a的方法
> >  void (*__Block_byref_id_object_dispose)(void*);     //释放成员UILabel *__strong a的方法
> >  UILabel *__strong a;
> > };
> >
> > //blockDataBlockFunction中__block初始化
> > static void _I_BlockStructureViewController_blockDataBlockFunction(BlockStructureViewController * self, SEL _cmd) 
> > { 
> >  /*初始化__block结构体参数如下：
> >    __forwarding = &a，
> >    __flags = 33554432,
> >    __Block_byref_id_object_copy = __Block_byref_id_object_copy_131，
> >    __Block_byref_id_object_dispose = __Block_byref_id_object_dispose_131；
> >  */
> >   __attribute__((__blocks__(byref))) __Block_byref_a_0 a 
> >    {
> >       (void*)0,
> >       (__Block_byref_a_0 *)&a, 
> >       33554432, 
> >       sizeof(__Block_byref_a_0), 
> >       __Block_byref_id_object_copy_131, 
> >       __Block_byref_id_object_dispose_131, 1
> >       ((UILabel *(*)(id, SEL))(void *)objc_msgSend)((id)objc_getClass("UILabel"), sel_registerName("new"))
> >    }; 
> >      
> >    ...
> > }
> >
> > //7. 内部调用block源码_Block_object_assign方法拷贝结构体成员a
> > //131=128+3; BLOCK_FIELD_IS_OBJECT+BLOCK_BYREF_CALLER
> > static void __Block_byref_id_object_copy_131(void *dst, void *src) 
> > {
> >  //8. 结构体指针偏移40bit,指向UILabel *__strong a成员
> >  _Block_object_assign((char*)dst + 40, *(void * *) ((char*)src + 40), 131);
> > }
> >
> > //内部调用block源码_Block_object_dispose方法释放结构体成员a
> > static void __Block_byref_id_object_dispose_131(void *src) 
> > {
> > //结构体指针偏移40bit,指向UILabel *__strong a成员
> >  _Block_object_dispose(*(void * *) ((char*)src + 40), 131);
> > }
> >
> > //8. mrc下调用 block源码
> > void _Block_object_assign(void *destArg, const void *object, const int flags) {
> >     
> >     const void **dest = (const void **)destArg;
> >     switch ((flags & BLOCK_ALL_COPY_DISPOSE_FLAGS)) {
> >       ...
> >       
> >       case BLOCK_BYREF_CALLER | BLOCK_FIELD_IS_OBJECT:
> >       case BLOCK_BYREF_CALLER | BLOCK_FIELD_IS_BLOCK:
> >         // 在mrc的情况下，你对对象添加__block， block是不会对这个对象引用计数+1
> >         *dest = object;
> >         break;
> >        
> >        ...
> >     }
> > }
> > ```
> >
> > **总结：**
> >
> > 1.\_\_block修饰的变量在编译后成为结构体struct \_\_Block\_byref\_a\_0。
> >
> > 2.\_\_block修饰的变量成为结构体的成员。与block回调方法中使用局部变量的规律类似：
> >
> > ```
> > 2.1 变量为基础类型时，结构体新增同名的基础类型成员，此成员保存初始化是变量的值。
> >
> > 2.2 变量为对象类型时，结构体新增strong修饰的对象类型成员，此成员前引用变量所指向的对象地址。
> >
> > 2.3 变量使用\_\_weak修饰时，结构体的成员也\_\_weak修饰。
> > ```
> >
> > 3.\_\_Block\_byref\_a\_0 \*\_\_forwarding：当结构体只在栈内时指针指向自己；当结构体在被复制到堆上时，栈内和堆内的结构体都指向对内的结构体。
> >
> > 4.当变量为对象类型时，结构体会增加copy 和 dispose方法，在mrc下这些方法来实现新增成员的复制和释放。



