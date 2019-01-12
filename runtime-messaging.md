#### Messaging

本章介绍如何将消息表达式转换为objc\_msgSend函数调用，以及如何按名称引用方法。然后，它解释了如何利用objc\_msgSend，以及如何 - 如果需要 - 您可以绕过动态绑定。

#### The objc\_msgSend Function

> 在Objective-C中，消息在运行时之前不会绑定到方法实现。 编译器会将消息表达式\[receiver message\]转换为对消息传递函数objc\_msgSend的调用objc\_msgSend。该函数会有2个默认参数，分别是 receiver 和 selector 。当然，若是该函数有其他的参数，那么其他参数就跟在2个默认参数后面。
>
> ```
> objc_msgSend(receiver, selector, arg1, arg2, ...)
> ```
>
> 在动态绑定过程中，objc\_msgSend 函数的作用如下
>
> 1. 首先找到selector对应的方法实现。由于相同的方法可以由不同的类以不同的方式实现，因此找到方法实现需要receiver
>
> 2. 调用方法实现，传入参数
>
> 3. 接收方法实现的返回值并将这个返回值作为自己的返回值
>
> 消息传递的关键在于编译器为每个类和对象构建的结构。每个类结构都包含这两个基本元素
>
> 1. 指向superclass的指针,即isa指针。isa的指针使对象可以访问它的类，并通过该类访问它继承的所有类。
> 2. 方法调度表（_dispatch table_） 。这个表具有将方法选择器selector与方法实现imp地址相关联的条目， 比如 sestOrigin:: 的 selector 对应的是 setOrigin::的方法实现地址。
>
>    ```
>                                ![](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Art/messaging1.gif)
>    ```
>
> 当消息发送到对象时，消息传递函数遵循对象的isa指向类结构的指针，在调度表中查找方法选择器。如果该选择器，objc\_msgSend尝试在superclass其调度表中寻找。如果还是没有找到，就会沿着类继承层次依次寻找superclass直到 NSObject。一旦找到了 method selector,那么就调用 method selector 对应的方法实现并传入对应的参数。这就是 runtime 寻找方法实现的方式，消息动态绑定到方法实现。
>
> 为了加速消息传递过程，消息被首次使用的时候运行时系统会缓存方法的选择器和地址。每个类都有一个单独的缓存，它可以包含**继承方法**以及类中定义的方法的选择器。消息传递的时候会先检查对象类的缓存（理论上一个方法被使用后可能会再次使用），如果有缓存就直接使用，如果没有再去搜索调度表。如果方法选择器在缓存中，消息传递只比函数调用稍微慢一点。一旦程序运行了足够长的时间来“预热”其缓存，它发送的几乎所有消息都会找到一个缓存的方法。 随着程序的运行，缓存会动态增长以容纳新消息。

#### Using Hidden Arguments

> 当objc msgSend找到方法实现imp时，它调用该方法实现并将消息中的所有参数传递给它。它还向方法实现传递两个隐藏参数：
>
> * the receiving object
>
> * The selector for the method
>
>   它们被称为“隐藏”，因为它们未在定义方法的源代码中声明。 它们在编译代码时插入到实现中。虽然这些参数没有显式声明，但源代码仍然可以引用它们（就像它可以引用接收对象的实例变量一样）。 方法将the receiving object称为self，并将其自身的selector称为\_cmd。 在下面的示例中，\_cmd引用strange方法的选择器，并指向接收奇怪消息的对象的self。
>
> ```
> - strange
> {
>     id  target = getTheReceiver();
>     SEL method = getTheMethod();
>  
>     if ( target == self || method == _cmd )
>         return nil;
>     return [target performSelector:method];
> }
> ```

#### Getting a Method Address

> 规避动态绑定的唯一方法是获取方法的地址并直接调用它，就好像它是一个函数一样。 这种情况可能适用于极少数情况下，当特定方法将连续多次执行，并且您希望每次执行方法时都避免消息传递的开销。
>
> 使用NSObject类中的methodForSelector：方法可以获取该selector的方法实现指针，然后使用指针调用该方法实现。 methodForSelector：返回的指针必须小心地转换为正确的函数类型。 返回和参数类型都应包含在强制转换中。
>
> 下面的示例显示了如何调用实现setFilled：方法的方法实现imp：
>
> ```
> void (*setter)(id, SEL, BOOL); 
> int i;
>
> setter = (void (*)(id, SEL, BOOL))[target methodForSelector:@selector(setFilled:)];
>
> for ( i = 0 ; i < 1000 ; i++ )
>
>     setter(targetList[i], @selector(setFilled:), YES);
> ```
>
> 传递给过程的前两个参数是接收对象（self）和方法选择器（\_cmd）。 这些参数隐藏在方法语法中，但在将方法作为函数调用时必须使其显式化。
>
> 使用methodForSelector：绕过动态绑定可以节省消息传递所需的大部分时间。 但是，只有在特定消息重复多次的情况下，节省才会显着，如上面所示的for循环。
>
> 注意，methodForSelector：由Cocoa运行时系统提供; 它不是Objective-C语言本身的一个特性。

#### 参考链接

> [https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html\#//apple\_ref/doc/uid/TP40008048-CH104-SW1](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html#//apple_ref/doc/uid/TP40008048-CH104-SW1)

#### 



