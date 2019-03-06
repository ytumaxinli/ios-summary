typedef struct CF\_BRIDGED\_MUTABLE\_TYPE\(id\) \_\_CFRunLoop \* CFRunLoopRef;

> ```
> struct __CFRunLoop {
>     pthread_t _pthread;
>     CFMutableSetRef _commonModes;
>     CFMutableSetRef _commonModeItems;
>     CFRunLoopModeRef _currentMode;
>     CFMutableSetRef _modes;
>     ...
> };
> ```

typedef struct\_\_CFRunLoopMode \*CFRunLoopModeRef;

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

typedef struct CF\_BRIDGED\_MUTABLE\_TYPE\(id\) \_\_CFRunLoopSource \* CFRunLoopSourceRef;

typedefstruct CF\_BRIDGED\_MUTABLE\_TYPE\(id\) \_\_CFRunLoopObserver \* CFRunLoopObserverRef;

typedefstruct CF\_BRIDGED\_MUTABLE\_TYPE\(NSTimer\) \_\_CFRunLoopTimer \* CFRunLoopTimerRef;



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
>     CFRunLoopRef newLoop = __CFRunLoopCreate(t);
>         __CFLock(&loopsLock);
>     loop = (CFRunLoopRef)CFDictionaryGetValue(__CFRunLoops, pthreadPointer(t));
>     if (!loop) {
>         CFDictionarySetValue(__CFRunLoops, pthreadPointer(t), newLoop);
>         loop = newLoop;
>     }
>         // don't release run loops inside the loopsLock, because CFRunLoopDeallocate may end up taking it
>         __CFUnlock(&loopsLock);
>     CFRelease(newLoop);
>     }
>     if (pthread_equal(t, pthread_self())) {
>         _CFSetTSD(__CFTSDKeyRunLoop, (void *)loop, NULL);
>         if (0 == _CFGetTSD(__CFTSDKeyRunLoopCntr)) {
>             _CFSetTSD(__CFTSDKeyRunLoopCntr, (void *)(PTHREAD_DESTRUCTOR_ITERATIONS-1), (void (*)(void *))__CFFinalizeRunLoop);
>         }
>     }
>     return loop;
> }
> ```



