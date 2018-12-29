#### block的类名及继承关系

> **打印block类名及继承关系**
>
> ```
> - (void)main 
> {
>     int a = 100;
>     //不使用外部变量
>     void (^ block_0)(int a) = ^(int a){
>         NSLog(@"a = %d",a);
>     };
>
>     //使用局部外部变量 与 __weak修饰符
>     __weak void (^ block_1)(void) = ^{
>         NSLog(@"a = %d",a);
>     };
>     
>     //使用局部外部变量 与 不使用修饰符
>     void (^ block_2)(void) = ^{
>         NSLog(@"a = %d",a);
>     };
>     
>     //使用局部外部变量 与 __strong修饰符
>     __strong void (^ block_3)(void) = ^{
>         NSLog(@"a = %d",a);
>     };
>     
>     //打印block_0、block_1、block_2、block_3的类名及继承关系
>     Class blockClass = object_getClass(block_0);
>     NSLog(@"blockClass %@", blockClass);
>     Class blockSuperClass = [blockClass superclass];
>     NSLog(@"blockSuperClass %@", blockSuperClass);
>     while (blockSuperClass) {
>         blockSuperClass = [blockSuperClass superclass];
>         NSLog(@"blockSuperClass %@", blockSuperClass); 
>     }
> }
> ```
>
> **打印结果**
>
> ```
> 2018-12-25 15:06:31.478418+0800 Block内存[575:19343] blockClass __NSGlobalBlock__
> 2018-12-25 15:06:31.478552+0800 Block内存[575:19343] blockSuperClass __NSGlobalBlock
> 2018-12-25 15:06:31.478610+0800 Block内存[575:19343] blockSuperClass NSBlock
> 2018-12-25 15:06:31.478661+0800 Block内存[575:19343] blockSuperClass NSObject
>
> 2018-12-25 15:01:00.961232+0800 Block内存[545:17511] blockClass __NSStackBlock__
> 2018-12-25 15:01:00.961476+0800 Block内存[545:17511] blockSuperClass __NSStackBlock
> 2018-12-25 15:01:00.961562+0800 Block内存[545:17511] blockSuperClass NSBlock
> 2018-12-25 15:01:00.961641+0800 Block内存[545:17511] blockSuperClass NSObject
>
> 2018-12-26 14:50:00.693276+0800 Block内存[4123:234809] blockClass __NSMallocBlock__
> 2018-12-26 14:50:00.693411+0800 Block内存[4123:234809] blockSuperClass __NSMallocBlock
> 2018-12-26 14:50:00.693468+0800 Block内存[4123:234809] blockSuperClass NSBlock
> 2018-12-26 14:50:00.693519+0800 Block内存[4123:234809] blockSuperClass NSObject
>
> 2018-12-25 14:33:56.442816+0800 Block内存[431:12598] blockClass __NSMallocBlock__
> 2018-12-25 14:33:56.443027+0800 Block内存[431:12598] blockSuperClass __NSMallocBlock
> 2018-12-25 14:33:56.443093+0800 Block内存[431:12598] blockSuperClass NSBlock
> 2018-12-25 14:33:56.443148+0800 Block内存[431:12598] blockSuperClass NSObject
> ```
>
> **总结**
>
> 1.以上代码中通过打印，可以看出block有三种类继承关系。
>
> **全局区： \_\_NSGlobalBlock\_**_**  **_**-&gt;  \_\_NSGlobalBlock  -&gt;  NSBlock  -&gt;  NSObject**
>
> **栈区：    \_\_NSStackBlock\_\_  -&gt; \_\_NSStackBlock  -&gt;  NSBlock  -&gt;  NSObject**
>
> **堆区：    \_\_NSMallocBlock\_\_  -&gt;  \_\_NSMallocBlock  -&gt;  NSBlock  -&gt;  NSObject**
>
> 2.block默认使用strong修饰符，block不使用修饰符与使用strong修饰符效果相同

**Block的类型与外部变量及修饰符之间的关系**

> **在不使用修饰符和使用strong、copy、weak三种修饰的block中，通过不使用外部变量、使用全局外部变量、静态全局外部变量、静态局部外部变量、局部外部变量和 **_**成员变量 **_**来分析block类的类型。**
>
> ```
> typedef void(^testBlock) (int a);
> @property(nonatomic, strong)testBlock strongBlock;
> @property(nonatomic, copy)  testBlock copyBlock;
> @property(nonatomic, weak)  testBlock weakBlock;
> @property(nonatomic, assign)int variable;
>
> static int variable_0 = 10000;
> int variable_1 = 10000;
>
> - (void)blockMemory 
> {
>     static int variable_2 = 10000;
>     int variable_3 = 10000; 
>     
>     __weak typeof(self) wself = self;
>
>     void (^ block)(void) = ^{
>         NSLog(@"variable %d", variable_2);
>     };
>     
>     _strongBlock = ^(int a) {
>         NSLog(@"variable %d", variable_2);
>     };
>     
>     _copyBlock = ^(int a){
>         NSLog(@"variable %d", variable_2);
>     };
>     
>     _weakBlock = ^(int a){
>         NSLog(@"variable %d", variable_2);
>     };
>     
>     NSLog(@"block:%@, strongBlock:%@, copyBlock:%@, weakBlock:%@",
>           object_getClass(block),object_getClass(_strongBlock), object_getClass(_copyBlock), object_getClass(_weakBlock));
> }
> ```
>
> **打印结果**
>
> ```
> block:__NSGlobalBlock__, strongBlock:__NSGlobalBlock__, copyBlock:__NSGlobalBlock__, weakBlock:__NSGlobalBlock__
> ```
>
> ```
> block:__NSMallocBlock__, strongBlock:__NSMallocBlock__, copyBlock:__NSMallocBlock__, weakBlock:__NSStackBlock__
> ```
>
> **总结**
>
> > 1. 不使用外部变量 或 使用全局变量、外部静态变量（全局/局部）时block的，无论使用什么修饰符block类型都为**\_\_NSGlobalBlock\_\_**，即全局区block。
> > 2. 使用 非全局变量 或 外部静态变量（全局/局部）时：
> > 3. > 1 不使用修饰符 和 使用strong、copy、weak三种修饰符的block均为\_\_NSMallocBlock\_\_，即堆区block；
> >    > 2 使用weak修饰符的block为\_\_NSStackBlock，即栈区block；
> >    > 3 只实现不进行等号赋值的block也\_\_NSStackBlock,即为栈区。如`NSLog(@"%@",^{NSLog(@"variable:%d",variable_3);});`





