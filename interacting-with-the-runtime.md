Objective-C程序在三个不同的级别与运行时系统交互：通过Objective-C源代码; 通过Foundation框架的NSObject类中定义的方法; 并通过直接调用运行时函数。

#### Objective-C Source Code

> 在大多数情况下，运行时系统会在后台自动运行。 您只需编写和编译Objective-C源代码即可使用它。
>
> 当您编译包含Objective-C类和方法的代码时，编译器将创建实现该语言的动态特征的数据结构和函数调用。数据结构捕获类和类别定义以及协议声明中的信息;它们包括在[The Objective-C Programming Language](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html#//apple_ref/doc/uid/TP30001163)的 [Defining a Class](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjectiveC/Chapters/ocDefiningClasses.html#//apple_ref/doc/uid/TP30001163-CH12) and [Protocols](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjectiveC/Chapters/ocProtocols.html#//apple_ref/doc/uid/TP30001163-CH15) 中讨论的类和协议对象、方法选择器、实例变量模板，以及从源代码中提取的其他信息主要运行时函数是发送消息的函数，如[Messaging](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html#//apple_ref/doc/uid/TP40008048-CH104-SW1)中所述。它由源代码消息表达式调用。

#### NSObject Methods

> Cocoa中的大多数对象都是NSObject类的子类，因此大多数对象都继承了它定义的方法。（值得注意的例外是NSProxy类;有关更多信息，请参阅[Message Forwarding](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtForwarding.html#//apple_ref/doc/uid/TP40008048-CH105-SW1)）。因此，NSObject的方法建立了每个实例和每个类对象固有的行为。 但是，在少数情况下，NSObject类仅定义了应该如何完成某事的模板。它本身并没有提供所有必需的代码。
>
> 例如，NSObject类定义一个描述实例方法，该方法返回描述类内容的字符串。这主要用于调试GDB print-object命令打印该方法返回的字符串。NSObject对此方法的实现不知道该类包含什么，因此它返回一个包含该对象的名称和地址的字符串。 NSObject的子类可以实现此方法以返回更多详细信息。例如，Foundation类NSArray返回它包含的对象的描述列表。
>
> 一些NSObject方法只是查询运行时系统的信息。 这些方法允许对象执行内省。例如这样一些类方法，它要求对象识别它的类`isKindOfClass:`and`isMemberOfClass:`测试对象在继承层次结构中的位置;`respondsToSelector:`表示对象是否可以接受特定消息;`conformsToProtocol:`它指示对象是否声明要实现在特定协议中定义的方法；`methodForSelector:`它提供了方法实现的地址。这样的方法使对象能够自省。

#### Runtime Functions

> 运行时系统是一个动态共享库，其公共接口由位于/usr/include/objc目录中的头文件中的一组函数和数据结构组成。其中许多函数允许您使用纯C来复制编写Objective-C代码时编译器所做的事情，其他的则构成了通过NSObject类的方法导出的功能的基础。这些函数可以开发运行时系统的其他接口，并生成可以增强开发环境的工具。在Objective-C中编程时不需要它们。但是，在编写Objective-C程序时，一些运行时函数有时可能会有用。所有这些函数都记录在 [Objective-C Runtime Reference](https://developer.apple.com/documentation/objectivec/objective_c_runtime).



