#### block的类名及继承关系

> **打印block类名及继承关系**
>
> ```
> - (void)main 
> {
>     int a = 100;
>     void (^ block_0)(void) = ^{
>         NSLog(@"a = %d",a);
>     };
>
>     __weak void (^ block_1)(void) = ^{
>         NSLog(@"a = %d",a);
>     };
>     
>     __strong void (^ block_2)(void) = ^{
>         NSLog(@"a = %d",a);
>     };
>
>     //打印block_0、block_1、block_2的类名及集成关系
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
> 2018-12-25 14:33:56.442816+0800 Block内存[431:12598] blockClass __NSMallocBlock__
> 2018-12-25 14:33:56.443027+0800 Block内存[431:12598] blockSuperClass __NSMallocBlock
> 2018-12-25 14:33:56.443093+0800 Block内存[431:12598] blockSuperClass NSBlock
> 2018-12-25 14:33:56.443148+0800 Block内存[431:12598] blockSuperClass NSObject
> ```
>
> **总结**
>
> 以上代码中通过打印，可以看出block有三种类继承关系。
>
> **全局区： \_\_NSGlobalBlock\_**_**  **_**-&gt;  \_\_NSGlobalBlock  -&gt;  NSBlock  -&gt;  NSObject**
>
> **栈区：    \_\_NSStackBlock\_\_  -&gt; \_\_NSStackBlock  -&gt;  NSBlock  -&gt;  NSObject**
>
> **堆区：    \_\_NSMallocBlock\_\_  -&gt;  \_\_NSMallocBlock  -&gt;  NSBlock  -&gt;  NSObject**



**Block的类型与外部变量及修饰符之间的关系**

> **分别在由strong、copy、weak修饰的block中，通过不使用外部变量、使用全局外部变量、静态全局外部变量、静态局部外部变量、局部外部变量来分析block类的类型。**
>
> ```
> typedef void(^testBlock) (int a);
> @property(nonatomic, strong)testBlock strongBlock;
> @property(nonatomic, copy)  testBlock copyBlock;
> @property(nonatomic, weak)  testBlock weakBlock;
>
> static int variable_0 = 10000;
> int variable_1 = 10000;
>
> - (void)blockMemory 
> {
>     static int variable_2 = 10000;
>     int variable_3 = 10000; 
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
>     NSLog(@"strongBlock %@, copyBlock %@, weakBlock %@", object_getClass(_strongBlock), object_getClass(_copyBlock), object_getClass(_weakBlock));
> }
> ```
>
> **打印结果**
>
> ```
> [717:26061] strongBlock __NSGlobalBlock__, copyBlock __NSGlobalBlock__, weakBlock __NSGlobalBlock__
> ```
>
> ```
> [702:25403] strongBlock __NSMallocBlock__, copyBlock __NSMallocBlock__, weakBlock __NSStackBlock__
> ```
>
> **总结**
>
> 1. **由strong、copy、weak修饰的block中，不使用外部变量、使用全局外部变量、静态全局外部变量、静态局部外部变量时block的类型都为\_\_NSGlobalBlock\_\_，即block被拷贝到了全局区**
>
> 2. **由strong、copy、weak修饰的block中，使用局部变量时，block的类型分别为，\_\_NSMallocBlock\_\_，\_\_NSMallocBlock\_\_，\_\_NSStackBlock\_\_，即block被strong和copy修饰是被拷贝到了堆区，而weak修饰的未进行拷贝**
>
> 3. **block在初始化的时候都是在栈区，只是在一些情况下block被从栈区拷贝到了全局、堆区。**



