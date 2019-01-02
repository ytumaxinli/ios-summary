根据之前的总结在ARC模式下，当block中含有非静态局部变量且block未用\_\_weak修饰时，block会从栈中拷贝到堆中。这里通过block源码以及编译后的C++代码来分析一下block的copy过程。

#### **OC源码**

```
- (void)objcDataBlockFunction {

    NSObject *objcData = [[NSObject alloc] init];

    void (^objcDataBlock)(void) = ^{
        NSLog(@"对象类型局部变量, %@", objcData);
    };
    objcData = nil;
    objcDataBlock();
    NSLog(@"对象类型局部变量_1, %@", objcData);
}
```



#### **C++代码**

```
//编译后的objcDataBlockFunction方法
  Flags = 570425344
static void _I_BlockStructureViewController_objcDataBlockFunction(BlockStructureViewController * self, SEL _cmd) {

    NSObject *objcData = ((NSObject *(*)(id, SEL))(void *)objc_msgSend)((id)((NSObject *(*)(id, SEL))(void *)objc_msgSend)((id)objc_getClass("NSObject"), sel_registerName("alloc")), sel_registerName("init"));

    void (*objcDataBlock)(void) = ((void (*)())&__BlockStructureViewController__objcDataBlockFunction_block_impl_0((void *)__BlockStructureViewController__objcDataBlockFunction_block_func_0, &__BlockStructureViewController__objcDataBlockFunction_block_desc_0_DATA, objcData, 570425344));
    objcData = __null;
    ((void (*)(__block_impl *))((__block_impl *)objcDataBlock)->FuncPtr)((__block_impl *)objcDataBlock);
    NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_10, objcData);
}

//编译后的objcDataBlock，新增成员NSObject *__strong objcData;
struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0 {
  struct __block_impl impl;
  struct __BlockStructureViewController__objcDataBlockFunction_block_desc_0* Desc;
  NSObject *__strong objcData;
  __BlockStructureViewController__objcDataBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__objcDataBlockFunction_block_desc_0 *desc, NSObject *__strong _objcData, int flags=0) : objcData(_objcData) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};


//block描述结构体，这里与之前相比较多了两个函数指针，copy、dispose这两个方法在block的拷贝和释放时使用
static struct __BlockStructureViewController__objcDataBlockFunction_block_desc_0 
{
  size_t reserved;
  size_t Block_size;
  void (*copy)(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*, struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*);
  void (*dispose)(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*);
} __BlockStructureViewController__objcDataBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0), __BlockStructureViewController__objcDataBlockFunction_block_copy_0, __BlockStructureViewController__objcDataBlockFunction_block_dispose_0};


//block体内的逻辑代码编译生成的函数
static void __BlockStructureViewController__objcDataBlockFunction_block_func_0(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0 *__cself) 
{
  NSObject *__strong objcData = __cself->objcData; // bound by copy
  NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_9, objcData);
}

//block copy方法
static void __BlockStructureViewController__objcDataBlockFunction_block_copy_0(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*dst, struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*src) 
{
  _Block_object_assign((void*)&dst->objcData, (void*)src->objcData, 3/*BLOCK_FIELD_IS_OBJECT*/);
}

//block dispose方法
static void __BlockStructureViewController__objcDataBlockFunction_block_dispose_0(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*src) 
{
  _Block_object_dispose((void*)src->objcData, 3/*BLOCK_FIELD_IS_OBJECT*/);
}

```



#### **block copy源码分析**

```
//block拷贝方法
void *_Block_copy(const void *arg) { //栈区的block
    
    struct Block_layout *aBlock;

    if (!arg) return NULL;
    
    // The following would be better done as a switch statement
    aBlock = (struct Block_layout *)arg;
    
    //aBlock->flags = BLOCK_HAS_COPY_DISPOSE|BLOCK_USE_STRET
    
    if (aBlock->flags & BLOCK_NEEDS_FREE) {  //block在堆区，只要把block引用计数+1就可以了
        // latches on high
        latching_incr_int(&aBlock->flags);
        return aBlock;
    }else if (aBlock->flags & BLOCK_IS_GLOBAL) {   //block是全局的，返回本来的值就行了
        return aBlock;
    }else {
        // Its a stack block.  Make a copy.
        struct Block_layout *result = malloc(aBlock->descriptor->size);
        if (!result) return NULL;
        memmove(result, aBlock, aBlock->descriptor->size); // bitcopy first
            
        // 清空flags标示
        result->flags &= ~(BLOCK_REFCOUNT_MASK|BLOCK_DEALLOCATING);
        //设置引用计数 和 堆标示BLOCK_NEEDS_FREE
        result->flags |= BLOCK_NEEDS_FREE | 2;  // logical refcount 1   标记block是在堆区了，同时引用计数+1
        //
        _Block_call_copy_helper(result, aBlock);
        // Set isa last so memory analysis tools see a fully-initialized object.
        result->isa = _NSConcreteMallocBlock;
        return result;
    }
}

```

```
//_Block_call_copy_helper
static void _Block_call_copy_helper(void *result, struct Block_layout *aBlock)
{
    struct Block_descriptor_2 *desc = _Block_descriptor_2(aBlock);
    if (!desc) return;

    (*desc->copy)(result, aBlock); // do fixup
}
```

```
//调用编译出的block desc copy方法，将结构体新增的成员进行拷贝
static void __BlockStructureViewController__objcDataBlockFunction_block_copy_0(struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*dst, struct __BlockStructureViewController__objcDataBlockFunction_block_impl_0*src) 
{
  _Block_object_assign((void*)&dst->objcData, (void*)src->objcData, 3/*BLOCK_FIELD_IS_OBJECT*/);
}
```

```
//flags = 3/*BLOCK_FIELD_IS_OBJECT*/
void _Block_object_assign(void *destArg, const void *object, const int flags) {
    
    const void **dest = (const void **)destArg;
    switch ((flags & BLOCK_ALL_COPY_DISPOSE_FLAGS)) {
      case BLOCK_FIELD_IS_OBJECT:
        _Block_retain_object(object);  //object 引用计数+1
        *dest = object;  //赋值
        break;
        ...
        ...
    }
}

```

#### 

#### 总结

在block栈到堆得拷贝过程大体为：

1. 根据block结构体中，desc-&gt;Block\_size在堆上申请内存空间

2. 将原栈上的block结构体的内容拷贝到堆block内存中

3. 调用desc的copy方法，它的参数分别为：堆结构体成员地址、栈结构体成员值、成员的拷贝方式

4. desc的copy方法最终\_Block\_object\_assign方法来完成成员的引用计数增加/内存拷贝等



