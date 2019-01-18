#### **ARC文档中对dealloc过程的解释**

> [llvm官方的ARC文档](http://clang.llvm.org/docs/AutomaticReferenceCounting.html#dealloc)中对ARC下的dealloc过程做了简单说明，从中还是能找出些有用的信息
>
> > A class may provide a method definition for an instance method named`dealloc`. This method will be called after the final`release`of the object but before it is deallocated or any of its instance variables are destroyed. The superclass’s implementation of`dealloc`will be called automatically when the method returns.
>
> 大概意思是：**①dealloc方法在最后一次release后，实例变量（Ivars）销毁之前调用。**
>
>                        **②父类的dealloc的方法将在子类dealloc方法返回后自动调用。**
>
> > The instance variables for an ARC-compiled class will be destroyed at some point after control enters the dealloc method for the root class of the class. The ordering of the destruction of instance variables is unspecified, both within a single class and between subclasses and superclasses.
>
> 大概意思是：**①ARC下对象的实例变量在根类的delloc方法，即\[NSObject dealloc\]中释放（通常root class都是NSObject）**。
>
>                        ** ②变量释放顺序是不确定的（一个类内的不确定，子类和父类间也不确定，也就是说不用care释放顺序）**

#### NSObject的析构过程

> 通过apple的runtime源码，不难发现NSObject执行`dealloc`时调用`_objc_rootDealloc`继而调用`object_dispose`随后调用`objc_destructInstance`方法，前几步都是条件判断和简单的跳转，最后的这个函数如下：
>
> ```
> void *objc_destructInstance(id obj) 
> {
>     if (obj) {
>         // Read all of the flags at once for performance.
>         bool cxx = obj->hasCxxDtor();
>         bool assoc = obj->hasAssociatedObjects();
>
>         // This order is important.
>         if (cxx) object_cxxDestruct(obj);
>         if (assoc) _object_remove_assocations(obj);
>         obj->clearDeallocating();
>     }
>
>     return obj;
> }
> ```
>
> 简单明确的干了三件事：
>
> 1. 执行一个叫`object_cxxDestruct`的东西干了点什么事
> 2. 执行`_object_remove_assocations`去除和这个对象assocate的对象（常用于category中添加带变量的属性\)
> 3. 执行`objc_clear_deallocating`，清空引用计数表并清除弱引用表，将所有`weak`引用指nil（这也就是weak变量能安全置空的所在）

#### 探寻隐藏的.cxx\_destruct

> 上面找到的名为`object_cxxDestruct`的方法最终成为下面的调用：
>
> ```
> static void object_cxxDestructFromClass(id obj, Class cls)
> {
>     void (*dtor)(id);
>
>     // Call cls's dtor first, then superclasses's dtors.
>
>     for ( ; cls; cls = cls->superclass) {
>         if (!cls->hasCxxDtor()) return; 
>         dtor = (void(*)(id))
>             lookupMethodInClassAndLoadCache(cls, SEL_cxx_destruct);
>         if (dtor != (void(*)(id))_objc_msgForward_impcache) {
>             if (PrintCxxCtors) {
>                 _objc_inform("CXX: calling C++ destructors for class %s", 
>                              cls->nameForLogging());
>             }
>             (*dtor)(obj);
>         }
>     }
> }
> ```
>
> 代码也不难理解，沿着继承链逐层向上搜寻`SEL_cxx_destruct`这个selector，找到函数实现\(`void (*)(id)`\(函数指针\)并执行。搜索这个selector的声明，发现是名为`.cxx_destruct`的方法，以点开头的名字，我想和unix的文件一样，是有**隐藏**属性的

####  



