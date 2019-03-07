**typedef struct  \_\_CFRunLoop \* CFRunLoopRef;**

> ```
> struct __CFRunLoop {
>     pthread_t _pthread;
>     CFMutableSetRef _commonModes;
>     CFMutableSetRef _commonModeItems;
>     CFRunLoopModeRef _currentMode;
>     CFMutableSetRef _modes;
>     struct _block_item *_blocks_head;
>     struct _block_item *_blocks_tail;
>     ...
> };
> ```

**typedef struct\_\_CFRunLoopMode \*CFRunLoopModeRef;**

> ```
> struct __CFRunLoopMode {
>     CFStringRef _name;
>     CFMutableSetRef _sources0;
>     CFMutableSetRef _sources1;
>     CFMutableArrayRef _observers;
>     CFMutableArrayRef _timers;
>     ....
> };
> ```

**typedef struct \_\_CFRunLoopTimer \* CFRunLoopTimerRef;**

> ```
> struct __CFRunLoopTimer {
>     CFRuntimeBase _base;
>     uint16_t _bits;
>     pthread_mutex_t _lock;
>     CFRunLoopRef _runLoop;
>     CFMutableSetRef _rlModes;
>     CFAbsoluteTime _nextFireDate;
>     CFTimeInterval _interval;        /* immutable */
>     CFTimeInterval _tolerance;          /* mutable */
>     uint64_t _fireTSR;            /* TSR units */
>     CFIndex _order;            /* immutable */
>     CFRunLoopTimerCallBack _callout;    /* immutable */
>     CFRunLoopTimerContext _context;    /* immutable, except invalidation */
> };
> ```

**typedef struct  \_\_CFRunLoopSource \* CFRunLoopSourceRef;**

> ```
> struct __CFRunLoopSource {
>     CFRuntimeBase _base;
>     uint32_t _bits;
>     pthread_mutex_t _lock;
>     CFIndex _order;            /* immutable */
>     CFMutableBagRef _runLoops;
>     union {
>     CFRunLoopSourceContext version0;    /* immutable, except invalidation */
>         CFRunLoopSourceContext1 version1;    /* immutable, except invalidation */
>     } _context;
> };
> ```

**typedef struct  \_\_CFRunLoopObserver \* CFRunLoopObserverRef;**

> ```
> struct __CFRunLoopObserver {
>     CFRuntimeBase _base;
>     pthread_mutex_t _lock;
>     CFRunLoopRef _runLoop;
>     CFIndex _rlCount;
>     CFOptionFlags _activities;                 /* immutable */
>     CFIndex _order;                            /* immutable */
>     CFRunLoopObserverCallBack _callout;        /* immutable */
>     CFRunLoopObserverContext _context;         /* immutable, except invalidation */
> };
> ```

**CFRunLoopSourceContext**

> ```
> typedef struct {
>     CFIndex    version;
>     void *    info;
>     const void *(*retain)(const void *info);
>     void    (*release)(const void *info);
>     CFStringRef    (*copyDescription)(const void *info);
>     Boolean    (*equal)(const void *info1, const void *info2);
>     CFHashCode    (*hash)(const void *info);
>     void    (*schedule)(void *info, CFRunLoopRef rl, CFRunLoopMode mode);
>     void    (*cancel)(void *info, CFRunLoopRef rl, CFRunLoopMode mode);
>     //回调方法
>     void    (*perform)(void *info);
> } CFRunLoopSourceContext;
> ```

**CFRunLoopSourceContext1**

> ```
> typedef struct {
>     CFIndex    version;
>     void *    info;
>     const void *(*retain)(const void *info);
>     void    (*release)(const void *info);
>     CFStringRef    (*copyDescription)(const void *info);
>     Boolean    (*equal)(const void *info1, const void *info2);
>     CFHashCode    (*hash)(const void *info);
>     //端口
>     mach_port_t    (*getPort)(void *info);
>     //回调方法
>     void *    (*perform)(void *msg, CFIndex size, CFAllocatorRef allocator, void *info);
> } CFRunLoopSourceContext1;
> ```

**CFRunLoopObserverContext**

> ```
> typedef struct {
>     CFIndex    version;
>     void *    info;
>     const void *(*retain)(const void *info);
>     void    (*release)(const void *info);
>     CFStringRef    (*copyDescription)(const void *info);
> } CFRunLoopObserverContext;
> ```

runloop启动![](/assets/9910A1D6-778D-4620-BF75-9150CDF02E5C.png)

**CFRunLoopGetCurrent     CFRunLoop.c**

> ```
> CFRunLoopRef CFRunLoopGetCurrent(void) {
>     CHECK_FOR_FORK();
>     CFRunLoopRef rl = (CFRunLoopRef)_CFGetTSD(__CFTSDKeyRunLoop);
>     if (rl) return rl;
>     return _CFRunLoopGet0(pthread_self());
> }
>
> CF_EXPORT CFRunLoopRef _CFRunLoopGet0(pthread_t t) {
>     //默认为主线程
>     if (pthread_equal(t, kNilPthreadT)) {
>     t = pthread_main_thread_np();
>     }
>     __CFLock(&loopsLock);
>     
>     //没有存储runloop的全局字典时，先创建全局字典并创建主线程的runloop并放入全局字典
>     if (!__CFRunLoops) {
>         __CFUnlock(&loopsLock);
>     CFMutableDictionaryRef dict = CFDictionaryCreateMutable(kCFAllocatorSystemDefault, 0, NULL, &kCFTypeDictionaryValueCallBacks);
>     CFRunLoopRef mainLoop = __CFRunLoopCreate(pthread_main_thread_np());
>     CFDictionarySetValue(dict, pthreadPointer(pthread_main_thread_np()), mainLoop);
>     if (!OSAtomicCompareAndSwapPtrBarrier(NULL, dict, (void * volatile *)&__CFRunLoops)) {
>         CFRelease(dict);
>     }
>     CFRelease(mainLoop);
>         __CFLock(&loopsLock);
>     }
>     
>     //通过传入的线程作为Key在全局字典中获取runloop
>     CFRunLoopRef loop = (CFRunLoopRef)CFDictionaryGetValue(__CFRunLoops, pthreadPointer(t));
>     __CFUnlock(&loopsLock);
>     
>     //如果没有则进行创建
>     if (!loop) {
>         CFRunLoopRef newLoop = __CFRunLoopCreate(t);
>         __CFLock(&loopsLock);
>         loop = (CFRunLoopRef)CFDictionaryGetValue(__CFRunLoops, pthreadPointer(t));
>         if (!loop) {
>             CFDictionarySetValue(__CFRunLoops, pthreadPointer(t), newLoop);
>             loop = newLoop;
>         }
>         ....   
>     }
>     ...
>     return loop;
> }CFRunLoopPerformBlock
> ```

**CFRunLoopPerformBlock**

> ```
> void CFRunLoopPerformBlock(CFRunLoopRef rl, CFTypeRef mode, void (^block)(void)) {
>    ....
>     struct _block_item *new_item = (struct _block_item *)malloc(sizeof(struct _block_item));
>     new_item->_next = NULL;
>     new_item->_mode = mode;
>     new_item->_block = block;
>     if (!rl->_blocks_tail) {
>        //头尾指针为空时将头指针指向新添加的_block_item
>     rl->_blocks_head = new_item;
>     } else {
>        //将尾部指针指向的_block_item的next指向新添加的_block_item
>     rl->_blocks_tail->_next = new_item;
>     }
>     //替换尾部指针指向新加入的_block_item
>     rl->_blocks_tail = new_item;
>     __CFRunLoopUnlock(rl);
> }
> ```

**\_\_CFRunLoopDoBlocks**

> ```
> static Boolean __CFRunLoopDoBlocks(CFRunLoopRef rl, CFRunLoopModeRef rlm) { // Call with rl and rlm locked
>     struct _block_item *head = rl->_blocks_head;
>     struct _block_item *tail = rl->_blocks_tail;
>     CFSetRef commonModes = rl->_commonModes;
>     CFStringRef curMode = rlm->_name;
>     struct _block_item *prev = NULL;
>     //获取runloop的第一个block
>     struct _block_item *item = head;
>     //遍历runloop下的所有block
>     while (item) {
>         struct _block_item *curr = item;
>         item = item->_next;
>         Boolean doit = false;
>         if (CFStringGetTypeID() == CFGetTypeID(curr->_mode)) {
>             //如果block的mode是指定的mode 或者 是kCFRunLoopCommonModes
>         doit = CFEqual(curr->_mode, curMode) || (CFEqual(curr->_mode, kCFRunLoopCommonModes) && CFSetContainsValue(commonModes, curMode));
>         } else {
>         doit = CFSetContainsValue((CFSetRef)curr->_mode, curMode) || (CFSetContainsValue((CFSetRef)curr->_mode, kCFRunLoopCommonModes) && CFSetContainsValue(commonModes, curMode));
>     }
>     if (!doit) prev = curr;
>     if (doit) {
>         if (prev) prev->_next = item;
>         if (curr == head) head = item;
>         if (curr == tail) tail = prev;
>         void (^block)(void) = curr->_block;
>             CFRelease(curr->_mode);
>             free(curr);
>         if (doit) {
>                 __CFRUNLOOP_IS_CALLING_OUT_TO_A_BLOCK__(block);
>             did = true;
>         }
>             Block_release(block); // do this before relocking to prevent deadlocks where some yahoo wants to run the run loop reentrantly from their dealloc
>     }
>     }
>     __CFRunLoopLock(rl);
>     __CFRunLoopModeLock(rlm);
>     if (head) {
>     tail->_next = rl->_blocks_head;
>     rl->_blocks_head = head;
>         if (!rl->_blocks_tail) rl->_blocks_tail = tail;
>     }
>     return did;
> }
> ```
>
> 我们看到`doit`这个bool变量完全决定了当前block是否执行。默认他是No的，而他被置为true的条件就是`CFEqual(curr->_mode, curMode) || (CFEqual(curr->_mode, kCFRunLoopCommonModes) && CFSetContainsValue(commonModes, curMode))`。就是当前mode与制定mode相等或者当前mode为commonMode（此处为一个字符串）且commonMode（此处为一个集合，若有不懂，请看runLoop结构）这个集合中包含指定mode。
>
> 这是因为这个判断的存在才允许commondMode可以在任意Mode下执行。  
> 当然这是提交到runLoop里的代码块才会走到`__CFRunLoopDoBlocks`这个方法。

相同的，我们通过上述代码也可以知道，runLoop通过端口唤醒的事件需要通过\_\_CFRunLoopDoSource1和\_\_CFRunLoopDoTimers两个方法来调用。

**\_\_CFRunLoopDoSource1直接调用源事件runLoopSourceRef即可。**

> ```
> static Boolean __CFRunLoopDoSource1(CFRunLoopRef rl, CFRunLoopModeRef rlm, CFRunLoopSourceRef rls, mach_msg_header_t *msg, CFIndex size, mach_msg_header_t **reply) {    
>     Boolean sourceHandled = false;
>     if (__CFIsValid(rls)) {
>     __CFRunLoopSourceUnsetSignaled(rls);
>     __CFRunLoopSourceUnlock(rls);
>         __CFRunLoopDebugInfoForRunLoopSource(rls);
>         //调用source1的perform block
>         __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE1_PERFORM_FUNCTION__(rls->_context.version1.perform, msg, size, reply);
>     CHECK_FOR_FORK();
>     sourceHandled = true;
>     }
>     return sourceHandled;
> }
> ```

重点我们看一下Timer的实现，核心代码如下：

&gt;

参考文档：

> [https://www.jianshu.com/p/ec629063390f](https://www.jianshu.com/p/ec629063390f)      老司机出品——源码解析之RunLoop详解



