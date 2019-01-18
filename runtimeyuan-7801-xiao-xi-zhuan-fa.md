**lookUpImpOrForward**

> ```
> IMP lookUpImpOrForward(Class cls, SEL sel, id inst, 
>                        bool initialize, bool cache, bool resolver)
> {
>  retry:
>     methodListLock.lock();
>     //1 在缓存中寻找，找到后跳转到 done Try this class's cache.
>
>     //2 在类方法列表中寻找，找到后加入缓存跳转到done. Try this class's method lists.
>
>     //3 遍历继承结构在父类中寻找缓存和方法列表。找到后加入子类的方法缓存并调用
>
>     //4 未找到进入动态方法解析 No implementation found. Try method resolver once.
>     if (resolver && !triedResolver) {
>       runtimeLock.unlock();
>       _class_resolveMethod(cls, sel, inst); //调用动态方法解析
>       runtimeLock.lock();
>       triedResolver = YES; //防止再次调用方法解析
>       goto retry;
>     }
>
>     //5 仍找到imp进入消息转发 No implementation found, and method resolver didn't help. 
>     // Use forwarding.
>
>     imp = (IMP)_objc_msgForward_impcache;
>     cache_fill(cls, sel, imp, inst);
>
>  done:
>     runtimeLock.unlock();
>
>     return imp;
> }
> ```

**动态方法解析（\_class\_resolveMethod）**

> ```
> void _class_resolveMethod(Class cls, SEL sel, id inst)
> {
>     if (! cls->isMetaClass()) {
>         // try [cls resolveInstanceMethod:sel]
>         _class_resolveInstanceMethod(cls, sel, inst);
>     } 
>     else {
>         // try [nonMetaClass resolveClassMethod:sel]
>         // and [cls resolveInstanceMethod:sel]
>         _class_resolveClassMethod(cls, sel, inst);
>         if (!lookUpImpOrNil(cls, sel, inst, 
>                             NO/*initialize*/, YES/*cache*/, NO/*resolver*/)) 
>         {
>             _class_resolveInstanceMethod(cls, sel, inst);
>         }
>     }
> }
> ```



