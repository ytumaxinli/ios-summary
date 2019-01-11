#### Dynamic Method Resolution

> 在某些情况下，你可能希望动态提供方法的实现。例如，Objective-C声明的属性功能（参见[Declared Properties](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjectiveC/Chapters/ocProperties.html#//apple_ref/doc/uid/TP30001163-CH17) in [The Objective-C Programming Language](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html#//apple_ref/doc/uid/TP30001163)）包含@dynamic指令：
>
> ```
> @dynamic propertyName;
> ```
>
> 它告诉编译器将动态提供与属性关联的方法。
>
> 你可以实现方法resolveInstanceMethod：和resolveClassMethod：分别为实例和类方法动态提供给定选择器的实现。
>
> Objective-C方法只是一个C函数，至少需要两个参数 - self和\_cmd。你可以使用函数class\_addMethod将函数作为方法添加到类中。 因此，给出以下功能：
>
> ```
> void dynamicMethodIMP(id self, SEL _cmd) {
>     // implementation ....
> }
> ```
>
> 你可以使用resolveInstanceMethod:像这样动态地将其作为方法添加到类中\(调用 resolveThisMethodDynamically\)
>
> ```
> @implementation MyClass
> + (BOOL)resolveInstanceMethod:(SEL)aSEL
> {
>     if (aSEL == @selector(resolveThisMethodDynamically)) {
>           class_addMethod([self class], aSEL, (IMP) dynamicMethodIMP, "v@:");
>           return YES;
>     }
>     return [super resolveInstanceMethod:aSEL];
> }
> @end
> ```
>
> 转发方法（如[Message Forwarding](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtForwarding.html#//apple_ref/doc/uid/TP40008048-CH105-SW1)中所述）和dynamic method resolution在很大程度上是正交的。类有机会在转发机制启动之前动态解析方法。如果 [respondsToSelector:](https://developer.apple.com/library/archive/documentation/LegacyTechnologies/WebObjects/WebObjects_3.5/Reference/Frameworks/ObjC/Foundation/Protocols/NSObject/Description.html#//apple_ref/occ/intfm/NSObject/respondsToSelector:) or [instancesRespondToSelector:](https://developer.apple.com/library/archive/documentation/LegacyTechnologies/WebObjects/WebObjects_3.5/Reference/Frameworks/ObjC/Foundation/Classes/NSObject/Description.html#//apple_ref/occ/clm/NSObject/instancesRespondToSelector:) 方法被调用，则动态方法解析器有机会首先为选择器提供IMP。如果您实现 [resolveInstanceMethod:](https://developer.apple.com/documentation/objectivec/nsobject/1418500-resolveinstancemethod) 但希望通过转发机制实际转发特定选择器，则为这些选择器返回NO。

#### Dynamic Loading

> Objective-C程序可以在运行时加载和链接新类和类别。新代码被合并到程序中，并且与开始时加载的类和类别完全相同。动态加载可用于做许多不同的事情，。。。。



#### **参考链接：**

> [https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtDynamicResolution.html\#//apple\_ref/doc/uid/TP40008048-CH102-SW1](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtDynamicResolution.html#//apple_ref/doc/uid/TP40008048-CH102-SW1)



