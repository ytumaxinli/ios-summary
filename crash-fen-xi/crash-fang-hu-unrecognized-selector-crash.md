**Crash产生原因**

> unrecognized selector类型的crash在app众多的crash类型中占着比较大的成分，通常是因为一个对象调用了一个不属于它方法的方法导致的。

**方法调用流程**

> runtime中具体的方法调用流程大致如下：
>
> 1. 首先，在相应操作的对象中的缓存方法列表中找调用的方法，如果找到，转向相应实现并执行。
>
> 2. 如果没找到，在相应操作的对象中的方法列表中找调用的方法，如果找到，转向相应实现执行
>
> 3. 如果没找到，去父类指针所指向的对象中执行1，2.
>
> 4. 以此类推，如果一直到根类还没找到，转向拦截调用，走消息转发机制。
>
> 5. 如果没有重写拦截调用的方法，程序报错。

**拦截调用**

> 在一个函数找不到时，runtime提供了三种方式去补救：
>
> 1. 调用resolveInstanceMethod给个机会让类添加这个实现这个函数
>
> 2. 调用forwardingTargetForSelector让别的对象去执行这个函数
>
> 3. 调用forwardInvocation（函数执行器）灵活的将目标函数以其他形式执行。
>
> 如果都不中，调用doesNotRecognizeSelector抛出异常。

**unrecognized selector crash 防护方案**

> 既然可以补救，我们完全也可以利用消息转发机制来做文章。那么问题来了，在这三个步骤里面，选择哪一步去改造比较合适呢。
>
> **方案一**
>
> > 选择了第二步forwardingTargetForSelector来做文章。原因如下：
> >
> > resolveInstanceMethod需要在类的本身上动态添加它本身不存在的方法，这些方法对于该类本身来说冗余的forwardInvocation可以通过NSInvocation的形式将消息转发给多个对象，但是其开销较大，需要创建新的NSInvocation对象，并且forwardInvocation的函数经常被使用者调用，来做多层消息转发选择机制，不适合多次重写forwardingTargetForSelector可以将消息转发给一个对象，开销较小，并且被重写的概率较低，适合重写
> >
> > ```
> > - (id)forwardingTargetForSelector:(SEL)aSelector
> > {
> >     if ([NSStringFromClass([self class]) hasPrefix:@"_"] || [NSStringFromClass([self class]) hasPrefix:@"UIKeyboard"] || [self isKindOfClass:NSClassFromString(@"UITextInputController")]) {
> >         return nil;
> >     }
> >     CrashProtectHandler *handler = [[CrashProtectHandler alloc] init];
> >     handler.crashMessages =  [NSString stringWithFormat:@"CrashProtect: [%@ %p %@]: unrecognized selector sent to instance",NSStringFromClass([self class]),self,NSStringFromSelector(aSelector)];
> >     class_addMethod([handler class], aSelector, [handler methodForSelector:@selector(crashProtectCollectCrashes)], "v@:");
> >     return handler;
> > }
> >
> > + (Class)forwardingTargetForSelector:(SEL)aSelector
> > {
> >     if ([NSStringFromClass([self class]) hasPrefix:@"_"]) {
> >         return nil;
> >     }
> >     //类属性
> >     CrashProtectHandler.crashMessages = [NSString stringWithFormat:@"CrashProtect: [%@ %p %@]: unrecognized selector sent to instance",NSStringFromClass([self class]),self,NSStringFromSelector(aSelector)];
> >     
> >     class_addMethod(object_getClass([CrashProtectHandler class]), aSelector, [[CrashProtectHandler class] methodForSelector:@selector(crashProtectCollectCrashes)], "v@:");
> >     return [CrashProtectHandler class];
> > }
> >
> > @interface CrashProtectHandler : NSObject
> >
> > @property (class) NSString *crashMessages;
> > @property (nonatomic,copy) NSString *crashMessages;
> >
> > - (void)crashProtectCollectCrashes;
> > + (void)crashProtectCollectCrashes;
> >
> > @end
> >
> > #import "CrashProtectHandler.h"
> > @implementation CrashProtectHandler
> >
> > static NSString *_crashMessages = nil;
> >
> > - (void)crashProtectCollectCrashes
> > {
> >     NSLog(@"crashMessages:%@",self.crashMessages);
> > }
> >
> > + (void)crashProtectCollectCrashes
> > {
> >     NSLog(@"_crashMessages:%@",_crashMessages);
> > }
> >
> > + (void)setCrashMessages:(NSString *)crashMessages
> > {
> >     _crashMessages = crashMessages;
> > }
> >
> > + (NSString *)crashMessages
> > {
> >     return _crashMessages;
> > }
> >
> > @end
> > ```
> >
> > **缺陷：**
> >
> > 容易转发系统方法，如UIKeyboardxxx,UITextInputController和\_开头的系统类转发的系统方法会被拦截。因此需要判断剔除
>
> **方案二**
>
> > 在消息转发的最后一步拦截，缺点在再生成invocation后转发消耗较大
> >
> > ```
> > //拦截实例方法
> > - (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector
> > {
> >     return [NSMethodSignature signatureWithObjCTypes:"v@:"];
> > }
> >
> > - (void)forwardInvocation:(NSInvocation *)anInvocation
> > {
> >     CrashProtectHandler *handler = [[CrashProtectHandler alloc] init];
> >     handler.crashMessages =  [NSString stringWithFormat:@"CrashProtect: [%@ %p %@]: unrecognized selector sent to instance",NSStringFromClass([self class]),self,NSStringFromSelector(anInvocation.selector)];
> >     anInvocation.selector = @selector(crashProtectCollectCrashes);
> >     [anInvocation invokeWithTarget:handler];
> > }
> >
> > //拦截类方法
> > + (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector
> > {
> >     return [NSMethodSignature signatureWithObjCTypes:"v@:"];
> > }
> >
> > + (void)forwardInvocation:(NSInvocation *)anInvocation
> > {
> >     CrashProtectHandler.crashMessages = [NSString stringWithFormat:@"CrashProtect: [%@ %p %@]: unrecognized selector sent to instance",anInvocation.target,self,NSStringFromSelector(anInvocation.selector)];
> >     anInvocation.selector = @selector(crashProtectCollectCrashes);
> >     [anInvocation invokeWithTarget:[CrashProtectHandler class]];
> > }
> > ```



