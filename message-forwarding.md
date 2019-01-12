#### Message Forwarding

将消息发送到不处理该消息的对象是一个错误。 但是，在宣布错误之前，运行时系统为接收对象提供了第二次处理消息的机会



#### Forwarding

> 如果向不处理该消息的对象发送消息，那么在宣布错误之前，运行时将向该对象发送一个forwardInvocation:消息并带有一个NSInvocation对象作为参数，这个NSInvocation对象封装了原始消息和随之传递的参数。
>
> 你可以实现forwardInvocation：方法来为消息提供默认响应，或以其他方式避免错误。顾名思义forwardInvocation：通常用于将消息转发给另一个对象。
>
> 要查看转发的范围和意图，请设想以下方案：首先，假设您正在设计一个可以响应名为negotiate的消息的对象，并且您希望其响应包含另一种对象的响应。您可以通过将negotiate消息传递给实现的negotiate方法的其他对象来轻松完成此操作。
>
> 更进一步，假设您希望对象对negotiate消息的响应完全是在另一个类中实现。
>
> ```
> - (void)forwardInvocation:(NSInvocation *)anInvocation
> {
>     if ([someOtherObject respondsToSelector:
>             [anInvocation selector]])
>         [anInvocation invokeWithTarget:someOtherObject];
>     else
>         [super forwardInvocation:anInvocation];
> }
> ```



#### Forwarding and Multiple Inheritance（转发和多重继承）

> 转发模仿继承，并可用于向Objective-C程序提供多重继承的一些效果。 如下所示，通过转发消息来响应消息的对象似乎借用或“继承”另一个类中定义的方法实现
>
> ![](/assets/Forwarding.png)
>
> 在此图示中，Warrior类的实例将negotiate消息转发给Diplomat类的实例。
>
> 转发消息的对象因此从继承层次结构的两个分支“继承”方法 - 它自己的分支和响应消息的对象的分支。 在上面的例子中，似乎Warrior类继承自Diplomat以及它自己的superclass。
>
> 转发提供了您通常希望从多个继承中获得的大多数功能。 但是，两者之间存在重要差异：**多重继承在单个对象中组合了不同的功能**。 它倾向于大型，多方面的对象。 另一方面，**转发将不同的对象分配给不同的对象。 它将问题分解为较小的对象**，而是以对消息发送者透明的方式将这些对象关联起来。



#### Surrogate Objects（代理对象）

> 虽然转发模仿继承，但NSObject类从不混淆这两者。 像respondsToSelector：和isKindOfClass这样的方法：**只查看继承层次结构**，而不是转发链。 例如，如果询问Warrior对象是否响应协商消息，
>
> ```
> if ( [aWarrior respondsToSelector:@selector(negotiate)] )
>     ...
> ```
>
> 答案是否定的，即使它可以毫无错误地接收谈判消息并在某种意义上通过将它们转发给negotiate来回应它们。
>
> 在许多情况下，NO是正确的答案。 但它可能不是。 如果使用转发来设置代理对象或扩展类的功能，则转发机制可能应该像继承一样透明。 如果您希望对象的行为就像它们真正继承了它们转发消息的对象的行为一样，那么您需要重新实现respondsToSelector：和isKindOfClass：方法来包含您的转发算法：
>
> ```
> - (BOOL)respondsToSelector:(SEL)aSelector
> {
>     if ( [super respondsToSelector:aSelector] )
>         return YES;
>     else {
>         /* Here, test whether the aSelector message can     *
>          * be forwarded to another object and whether that  *
>          * object can respond to it. Return YES if it can.  */
>     }
>     return NO;
> }
> ```
>
> 除了respondsToSelector:和isKindOfClass:之外，instancesRespondToSelector:方法也应该进行修改来包含转发算法。如果使用协议，也应该将conformsToProtocol:方法也应该进行相应修改。类似地，如果一个对象转发它收到的任何远程消息，也应该修改methodSignatureForSelector：让它可以返回最终响应转发消息的方法的准确描述。例如，如果一个对象能够将消息转发给它的surrogate，那么您将实现methodSignatureForSelector：如下所示：
>
> ```
> - (NSMethodSignature*)methodSignatureForSelector:(SEL)selector
> {
>     NSMethodSignature* signature = [super methodSignatureForSelector:selector];
>     if (!signature) {
>        signature = [surrogate methodSignatureForSelector:selector];
>     }
>     return signature;
> }
> ```
>
> 您可以考虑将转发算法放在私有代码的某个位置，并使用所有这些方法\(forwardInvocation: include\)调用它。



#### 

  


#### 



