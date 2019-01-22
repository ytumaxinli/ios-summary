#### **对象内存中布局**

> **示例一：NSObject 对象内存中布局（系统分配内存16）**![](/assets/OC对象本质.png)
>
> **示例二：Student 对象在内存中的分布（系统分配内存16）**![](/assets/OC对象本质.png)![](/assets/Student内存分布1.png)![](/assets/Student内存分布2.png)
>
> **示例三：person 和 student内存布局（系统分配内存16 16）**![](/assets/OC对象本质.png)![](/assets/instance内存布局3.png)

#### **查看内存布局方法**

> **方法定义**
>
> > ```
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
> > 4. // 将未对齐的内存大小进行对齐操作，从实现代码上可以看出对齐操作是让内存大小64位下为8的倍数、32位下为4的倍数
> >    static inline uint32_t word_align(uint32_t x) {
> >         return (x + WORD_MASK) & ~WORD_MASK;
> >    }
> >    
> > #ifdef __LP64__
> > #   define WORD_MASK 7UL
> > #else
> > #   define WORD_MASK 3UL
> > #endif
> > ```
> >
> > **总结：**
> >
> > class\_getInstanceSize方法获取的是内存对齐后的成员变量所占的内存大小，而不是分配的内存的大小。内存对齐的结果是64位下为8的倍数、32位下为4的倍数。

#### **alloc方法内存分配**

> **源码流程**
>
> ```
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
> **总结：内存大小分配遵循的原则是**
>
> 1.size\_t size = alignedInstanceSize\(\) + extraBytes;首先将成员变量大小进行内存对齐。
>
> 2.if \(size &lt; 16\) size = 16;如果对齐后内存大小小于16，则设置为16
>
> 3.void \*calloc\(size\_t num\_items, size\_t size\)，系统分配内存时也遵循内存对齐，内存需为对齐数（16）的倍数。\#define NANO\_MAX\_SIZE256 /\* Buckets sized {16, 32, 48, ..., 256} \*/

#### 

#### **内存查看**

> 方法一：Xcode-&gt;Debug-&gt;Debug Workflow-&gt;View Memory
>
> 方法二：LLDB指令![](/assets/LLDB指令.png)

#### 

#### **面试问题**

> 1 一个NSObject 对象占用多少内存？
>
> > 系统分配了NSObject 对象分配16个字节的内存（使用malloc\_size方法获得）
> >
> > 但NSObject对象内部只有一个成员变量isa,在64位环境下占用8字节（可以使用class\_getInstanceSize获得）



