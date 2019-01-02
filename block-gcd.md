#### **OC 源码**

```
- (void)gcdBlockFunction {
    
    NSObject *object = [NSObject new];
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            NSLog(@"gcd_object_1, %@",object);
        });
    });
    object = nil;
    NSLog(@"gcd_object_2, %@",object);
}

//运行结果
[1850:111512] gcd_object_2, (null)
[1850:111512] gcd_object_1, <NSObject: 0x283a4cd60>
```

#### 

#### C++代码

```
static void _I_BlockStructureViewController_gcdBlockFunction(BlockStructureViewController * self, SEL _cmd) 
{
    //初始化object对象
    NSObject *object = ((NSObject *(*)(id, SEL))(void *)objc_msgSend)((id)objc_getClass("NSObject"), sel_registerName("new"));
    
    //将外层block作为参数传入dispatch方法
    dispatch_after(dispatch_time((0ull), (int64_t)(1 * 1000000000ull)), dispatch_get_main_queue(), ((void (*)())&__BlockStructureViewController__gcdBlockFunction_block_impl_1((void *)__BlockStructureViewController__gcdBlockFunction_block_func_1, &__BlockStructureViewController__gcdBlockFunction_block_desc_1_DATA, object, 570425344)));
    
    object = __null;
   
     NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_20,object);
}

```

```
//外层block结构体。新增成员NSObject *__strong object，强引用object。
struct __BlockStructureViewController__gcdBlockFunction_block_impl_1 
{
  struct __block_impl impl;
  struct __BlockStructureViewController__gcdBlockFunction_block_desc_1* Desc;
  NSObject *__strong object;
  __BlockStructureViewController__gcdBlockFunction_block_impl_1(void *fp, struct __BlockStructureViewController__gcdBlockFunction_block_desc_1 *desc, NSObject *__strong _object, int flags=0) : object(_object) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
```

```
//外层block回调方法
static void __BlockStructureViewController__gcdBlockFunction_block_func_1(struct __BlockStructureViewController__gcdBlockFunction_block_impl_1 *__cself) 
{
  NSObject *__strong object = __cself->object; // bound by copy
  //将内层block作为参数传入dispatch方法
  dispatch_after(dispatch_time((0ull), (int64_t)(1 * 1000000000ull)), dispatch_get_main_queue(), ((void (*)())&__BlockStructureViewController__gcdBlockFunction_block_impl_0((void *)__BlockStructureViewController__gcdBlockFunction_block_func_0, &__BlockStructureViewController__gcdBlockFunction_block_desc_0_DATA, object, 570425344)));
}
```

```
//内层block结构体，。新增成员NSObject *__strong object，强引用object。
struct __BlockStructureViewController__gcdBlockFunction_block_impl_0 {
  struct __block_impl impl;
  struct __BlockStructureViewController__gcdBlockFunction_block_desc_0* Desc;
  NSObject *__strong object;
  __BlockStructureViewController__gcdBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__gcdBlockFunction_block_desc_0 *desc, NSObject *__strong _object, int flags=0) : object(_object) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
```

```
//内层block回调方法。
static void __BlockStructureViewController__gcdBlockFunction_block_func_0(struct __BlockStructureViewController__gcdBlockFunction_block_impl_0 *__cself) 
{
  NSObject *__strong object = __cself->object; // bound by copy

  NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_cc4365_mi_19,object);
}
```

#### 

#### 逻辑总结：

> 1.dispatch\_after会对block进行copy 和 release 
> ```
> void dispatch_after(dispatch_time_t when, dispatch_queue_t queue,dispatch_block_t block)；
>
> The block to submit. This function performs aBlock_copyandBlock_releaseon behalf of the caller.
> ```
>
> 2.内外block结构体都强持有object对象，最终传递到内层block回调方法中。





