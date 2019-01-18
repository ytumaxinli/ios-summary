**消息发送入口**

> 在runtimey源码中，消息发送的入口是用汇编编写的。去除无关逻辑后代码如下
>
> ```
> ENTRY _objc_msgSend
>
> 	cmp	p0, #0			// 检查消息接受者是否为空
> #if SUPPORT_TAGGED_POINTERS
> 	b.le	LNilOrTagged		// 如果为空跳转至LNilOrTagged
> #else
> 	b.eq	LReturnZero		// 如果为空跳转至LReturnZero
> #endif
> 	ldr	p13, [x0]		// p13 = isa 通过优化过的isa指针获取真实的isa之神
> 	GetClassFromIsa_p16 p13	// p16 = class 通过isa指针获取到类对象
> 	
> LGetIsaDone:				// 如果获取到了isa指针
> 	CacheLookup NORMAL		// 进入CacheLookup，此处$0=NORMAL。
>
> LNilOrTagged:
> 	b.eq	LReturnZero		// 如果消息接受者为空跳转至LReturnZero
> 	
> LReturnZero:
> 			
> END_ENTRY _objc_msgSend
> ```

**缓存寻找（CacheLookup）**

> ```
> .macro CacheLookup				//类似c语言#define CacheLookup
> ...
> 	ldp	p17, p9, [x12]		// {imp, sel} = *bucket
> 1:	cmp	p9, p1			// if (bucket->sel != _cmd)
> 	b.ne	2f			//     scan more
> 	CacheHit $0			// 结果1：寻找到进入CacheHit.call or return imp
> 	
> 2:	// not hit: p12 = not-hit bucket
> 	CheckMiss $0			// 结果2：未寻找到进入CheckMiss. if bucket->sel == 0
> 	cmp	p12, p10		// wrap if bucket == buckets
> 	b.eq	3f
> 	ldp	p17, p9, [x12, #-BUCKET_SIZE]!	// {imp, sel} = *--bucket
> 	b	1b			// loop
> ...
>
> 3:	// double wrap
> 	JumpMiss $0			//结果3：未寻找到进入JumpMiss
> 	
> .endmacro
>
> ```

**缓存寻找到\(CacheHit\)**

> ```
> .macro CacheHit
> .if $0 == NORMAL			// 此处$0 == NORMAL
> 	TailCallCachedImp x17, x12	// 调用imp. authenticate and call imp
> .elseif $0 == GETIMP
> 	mov	p0, p17
> 	AuthAndResignAsIMP x0, x12	// authenticate imp and re-sign as IMP
> 	ret				// return IMP
> .elseif $0 == LOOKUP
> 	AuthAndResignAsIMP x17, x12	// authenticate imp and re-sign as IMP
> 	ret				// return imp via x17
> .else
> .abort oops
> .endif
> .endmacro
> ```

**缓存未寻找到一（CheckMiss）**

> ```
> .macro CheckMiss
> 	// miss if bucket->sel == 0
> .if $0 == GETIMP
> 	cbz	p9, LGetImpMiss
> .elseif $0 == NORMAL				//此处$0 == NORMAL
> 	cbz	p9, __objc_msgSend_uncached	//调用__objc_msgSend_uncached
> .elseif $0 == LOOKUP
> 	cbz	p9, __objc_msgLookup_uncached
> .else
> .abort oops
> .endif
> .endmacro
> ```

**缓存未寻找到二**

> ```
> .macro JumpMiss
> .if $0 == GETIMP
> 	b	LGetImpMiss			
> .elseif $0 == NORMAL			       //此处$0 == NORMAL
> 	b	__objc_msgSend_uncached       //调用__objc_msgSend_uncached
> .elseif $0 == LOOKUP
> 	b	__objc_msgLookup_uncached
> .else
> .abort oops
> .endif
> .endmacro
> ```

**\_\_objc\_msgSend\_uncached**

> ```
> STATIC_ENTRY __objc_msgSend_uncached
> UNWIND __objc_msgSend_uncached, FrameWithNoSaves
>
> // THIS IS NOT A CALLABLE C FUNCTION
> // Out-of-band p16 is the class to search
> 	
> MethodTableLookup				//方法列表中寻找
> TailCallFunctionPointer x17
>
> END_ENTRY __objc_msgSend_uncached
> ```

**方法列表寻找**

> ```
> .macro MethodTableLookup
> 	...
> 	bl	__class_lookupMethodAndLoadCache3 //调用c方法_class_lookupMethodAndLoadCache3
> 	...
>
> .endmacro
> ```

**C方法 \_class\_lookupMethodAndLoadCache3**

> ```
> IMP _class_lookupMethodAndLoadCache3(id obj, SEL sel, Class cls)
> {
>     return lookUpImpOrForward(cls, sel, obj, 
>                               YES/*initialize*/, NO/*cache*/, YES/*resolver*/);
> }
>
>
> IMP lookUpImpOrForward(Class cls, SEL sel, id inst, 
>                        bool initialize, bool cache, bool resolver)
> {
>    runtimeLock.assertUnlocked(); 
>
>     //1 在缓存中寻找，找到后跳转到 done Try this class's cache.
>     imp = cache_getImp(cls, sel);
>     if (imp) goto done;
>
>     //2 在类方法列表中寻找，找到后加入缓存跳转到done. Try this class's method lists.
>     {
>         Method meth = getMethodNoSuper_nolock(cls, sel);
>         if (meth) {
>             log_and_fill_cache(cls, meth->imp, sel, inst, cls);
>             imp = meth->imp;
>             goto done;
>         }
>     }
>
>     //3 遍历继承结构在父类中寻找缓存和方法列表。找到后加入子类的方法缓存并调用
>     {
>         unsigned attempts = unreasonableClassCount();
>         for (Class curClass = cls->superclass; curClass != nil; curClass = curClass->superclass) 
>         {
>             // Superclass cache.
>             imp = cache_getImp(curClass, sel);
>             if (imp) {
>                 if (imp != (IMP)_objc_msgForward_impcache) {
>                     log_and_fill_cache(cls, imp, sel, inst, curClass);//方法加入到子类方法缓存
>                     goto done;
>                 }else {
>                     break;
>                 }
>             }
>             // Superclass method list.
>             Method meth = getMethodNoSuper_nolock(curClass, sel);
>             if (meth) {
>                 log_and_fill_cache(cls, meth->imp, sel, inst, curClass);//方法加入到子类方法缓存
>                 imp = meth->imp;
>                 goto done;
>             }
>         }
>     }
>
>     //4 未找到进入动态方法解析 No implementation found. Try method resolver once.
>     if (resolver  &&  !triedResolver) {
>         runtimeLock.unlock();
>         _class_resolveMethod(cls, sel, inst);    //调用动态方法解析
>         runtimeLock.lock();
>         triedResolver = YES;                     //防止再次调用方法解析
>         goto retry;
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

**方法调用流程总结**

> 判断方法调用者是否为nil,如果为nil直接返回结束\_objc\_msgsend\(id self, SEL selector\)方法调用。**（ \_objc\_msgSend汇编方法中）**
>
> 通过isa指针寻找到类。**（ \_objc\_msgSend汇编方法中）**
>
> 在类的缓存中类的缓存中寻找IMP，进入CacheHit调用IMP.  **\(CacheLookup汇编方法中\)**
>
> 去类的方法列表中寻找，寻找到加入该类的缓存列表中并进行调用**（\_class\_lookupMethodAndLoadCache3）**
>
> 在遍历类的继承结构中寻找父类的缓存和方法列表，直至superclass为nil.如果找到将方法加入到子类的缓存中并调用**（\_class\_lookupMethodAndLoadCache3）**





