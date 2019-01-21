**NSObject 对象内存中布局**

> ![](/assets/OC对象本质.png)

**查看内存布局方法**

> **方法定义**
>
> > ```
> > //返回指针ptr的大小（Returns size of given ptr ）
> > size_t malloc_size(const void *ptr);
> >
> > //返回类的实例的大小（Returns the size of instances of a class.）
> > size_t class_getInstanceSize(Class cls);
> > ```
>
> **class\_getInstanceSize源码分析**
>
> > 源码调用顺序
> >
> > ```
> > 1. size_t class_getInstanceSize(Class cls)
> >     {
> >         if (!cls) return 0;
> >         return cls->alignedInstanceSize();
> >     }
> >
> > 2. // 返回类的成员变量的大小（Class's ivar size rounded up to a pointer-size boundary.）
> >     uint32_t alignedInstanceSize() {
> >         return word_align(unalignedInstanceSize());
> >     }
> >
> > 3. // 返回可能进行对齐操作的类的成员变量的大小（May be unaligned depending on class's ivars.）
> >    uint32_t unalignedInstanceSize() {
> >         assert(isRealized());
> >         //
> >         return data()->ro->instanceSize;
> >     }
> >
> > 4. // 将未对齐的内存大小进行对齐操作，从实现代码上可以看出对齐操作是让内存大小为8的倍数
> >    static inline uint32_t word_align(uint32_t x) {
> >         return (x + WORD_MASK) & ~WORD_MASK;
> >    }
> > ```
> >
> > 总结：
> >
> > class\_getInstanceSize方法获取的是内存对齐后的成员变量所占的内存大小，而不是分配的内存的大小。

**alloc方法内存分配**

> **源码流程**
>
> ```
> 1. obj = class_createInstance(cls, 0);
>
> 2. id class_createInstance(Class cls, size_t extraBytes)
>    {
>       return _class_createInstanceFromZone(cls, extraBytes, nil);
>    }
> 3. _class_createInstanceFromZone(Class cls, size_t extraBytes, void *zone, 
>                               bool cxxConstruct = true, 
>                               size_t *outAllocatedSize = nil)
> {
>     ...
>     //通过instanceSize方法获取到需要分配的内存大小（extraBytes==0）
>     size_t size = cls->instanceSize(extraBytes);
>     ...
>     //通过calloc方法进行初始化
>     obj = (id)calloc(1, size);
>     ...
>     return obj;
> }
> 4.  size_t instanceSize(size_t extraBytes) {
>         //此size及class_getInstanceSize方法获取到的对齐后的成员变量的大小。
>         size_t size = alignedInstanceSize() + extraBytes;
>         // 成员变量大小小于16，设置为16（CF requires all objects be at least 16 bytes.）
>         if (size < 16) size = 16;
>         return size;
>     }
> ```
>
> **总结**



#### **面试问题**

> 1 一个NSObject 对象占用多少内存？
> > 系统分配了NSObject 对象分配16个字节的内存（使用malloc\_size方法获得）
> >
> > 但NSObject内部只是用了8个字节（在64位下环境下，可以使用class\_getInstanceSize获得）



