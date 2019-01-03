#### block copy的情况：

> block作为函数返回值时  
> 将block赋值给\_\_strong指针时  
> block作为Cocoa API中方法名含有usingBlock的方法参数时  
> block作为GCD API的方法参数时

#### **block编译后分为两部分：**

**1.block结构体**

> 1.1 imp 结构体
>
> > 1.1.1  isa指针
> >
> > 1.1.2 flags block结构体的引用计数 以及 block的标示：BLOCK\_IS\_GLOBAL  BLOCK\_NEEDS\_FREE等。
> >
> > 1.1.3 func指针指向block回调方法
>
> 1.2 desc 结构体
>
> > 1.2.1 \_\_size:存储block结构体的size
> >
> > 1.2.2 copy方法 ：外部变量索引的拷贝方法----MRC下调用
> >
> > 1.2.3 dispose方法：外部变量索引的释放方法----MRC下调用
>
> 1.3 回调方法中外部变量的索引
>
> > 1.3.1 没有使用外部变量、外部变量为全局变量、全局静态变量时，没有此值
> >
> > 1.3.2 成员变量、属性时，强引用self
> >
> > 1.3.3 局部静态变量时，引用变量指针
> >
> > 1.3.3 \_\_block 变量时，引用\_\_Block\_byref结构体指针

**2.block回调方法**

> 2.1 回调方法参数为block对象
>
> 2.2 对于结构体中有外部变量的索引的情况，回调方法会通过block对象拿到引用的外部变量

#### \_\_block编译后成为结构体：

> 1.flags
>
> 2.\_\_forwarding
>
> > 2.1 block未被复制只在栈区时，\_\_forwarding指针指向自身
> >
> > 2.2 block被复制到堆区时,栈区堆区\_\_forwarding都指向栈区\_\_block结构体
>
> 3.size
>
> 4.变量引用
>
> 5.copy方法
>
> 6.dispose方法

#### block copy流程

> 1.调用\_Block\_copy方法，参数为原block
>
> 2.根据原block结构体重flags的值，判断是栈区？堆区？全局区？block
>
> 3.原block为栈区block是，根据size在堆上分配内存空间并拷贝内存中数据到堆
>
> 4.调用block结构体中的copy方法，copy外部变量索引
>
> 5.结构体的copy方法调用block源码\_Block\_object\_assign，并将copy方法中的外部变量索引类型传递给\_Block\_object\_assign方法
>
> 6.\_Block\_object\_assign根据外部变量索引类型对变量进行引用计数增加、直接复制或者拷贝等处理
>
> 7.如果引用参数为\_\_block 变量，则会调用\_Block\_byref\_copy  
> 8. 根据原栈上的\_Block\_byref结构体size在堆上分配内存，copy栈上内容到堆
>
> 9.修改栈和对上的\_Block\_byref结构体的forwarding指向堆\_Block\_byref
>
> 10.调用\_Block\_byref结构体的copy方法
>
> 11.结构体的copy方法调用block源码 \_Block\_object\_assign,，并将\_\_block 变量类型传递给\_Block\_object\_assign方法
>
> 12.\_Block\_object\_assign根据外部变量索引类型对变量进行引用计数增加、直接复制或者拷贝等处理













