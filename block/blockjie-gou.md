**OC代码转化为C++代码**

> **1.查看当前xcode正在使用的编译版本**
>
> ```
> //终端中输入xcodebuild -showsdks
> bogon:~ tsmc$ xcodebuild -showsdks
>
> //输出结果
> iOS SDKs:
>     iOS 12.1                          -sdk iphoneos12.1
>
> iOS Simulator SDKs:
>     Simulator - iOS 12.1              -sdk iphonesimulator12.1
> ```
>
> **2. 通过clang编译器将OC转化为C++代码命令**
>
> ```
> //ios
> xcrun -sdk iphonesimulator11.4 clang -S -rewrite-objc -fobjc-arc -fobjc-runtime=ios-11.4 文件名
>
> //mac
> xcrun -sdk macosx10.14 clang -S -rewrite-objc -fobjc-arc 文件名
> ```



**分析多种block转化后的C++代码**

* **空的block**

  > **OC源代码**
  >
  > ```
  > - (void)emptyBlockFunction {
  >     
  >     void (^emptyBlock)(void) = ^{
  >         NSLog(@"八点钟学院");
  >     };
  >     emptyBlock();
  > }
  > ```
  >
  > **C++代码**
  >
  > ```
  > //编译后的emptyBlockFunction方法
  > static void _I_BlockStructureViewController_emptyBlockFunction(BlockStructureViewController * self, SEL _cmd) 
  > {
  >     //通过调用构造方法__BlockStructureViewController__emptyBlockFunction_block_impl_0，得到emptyBlock函数指针。
  >     //参数1：函数指针：编译后block中的逻辑代码，被编译到了一个独立函数中，此函数指针即指向这个独立函数
  >     //参数2：__BlockStructureViewController__emptyBlockFunction_block_desc_0_DATA结构体地址，此结构体存储了emptyBlock为结构体的size
  >     void (*emptyBlock)(void) = ((void (*)())&__BlockStructureViewController__emptyBlockFunction_block_impl_0((void *)__BlockStructureViewController__emptyBlockFunction_block_func_0, &__BlockStructureViewController__emptyBlockFunction_block_desc_0_DATA));
  >     
  >     ((void (*)(__block_impl *))((__block_impl *)emptyBlock)->FuncPtr)((__block_impl *)emptyBlock);
  > }
  >
  > //转化后的emptyBlock为结构体
  > struct __BlockStructureViewController__emptyBlockFunction_block_impl_0 
  > {
  >   struct __block_impl impl;
  >   struct __BlockStructureViewController__emptyBlockFunction_block_desc_0* Desc;
  >   
  >   //结构体构造函数
  >   __BlockStructureViewController__emptyBlockFunction_block_impl_0(void *fp, struct __BlockStructureViewController__emptyBlockFunction_block_desc_0 *desc, int flags=0) {
  >     impl.isa = &_NSConcreteStackBlock;
  >     impl.Flags = flags;
  >     impl.FuncPtr = fp;
  >     Desc = desc;
  >   }
  > };
  >
  > struct __block_impl {
  >   void *isa;
  >   int Flags;      //与Block源码中Block_private.h中的枚举相对应。标示block是否被copy、是否为全局block、是否为堆block等
  >   int Reserved;
  >   void *FuncPtr;  //函数调用指针。编译后block中的逻辑代码被编译到了一个独立函数中，此函数指针用来指向和调用这个独立函数。
  > };
  >
  > static struct __BlockStructureViewController__emptyBlockFunction_block_desc_0 
  > {
  >   size_t reserved;
  >   size_t Block_size;
  > } __BlockStructureViewController__emptyBlockFunction_block_desc_0_DATA = { 0, sizeof(struct __BlockStructureViewController__emptyBlockFunction_block_impl_0)};
  >
  >
  > static void __BlockStructureViewController__emptyBlockFunction_block_func_0(struct __BlockStructureViewController__emptyBlockFunction_block_impl_0 *__cself) 
  > {
  >   NSLog((NSString *)&__NSConstantStringImpl__var_folders_s9_886c185n58l8zmt9rwkglcsc0000gn_T_BlockStructureViewController_dd128d_mi_0);
  > }
  > ```



