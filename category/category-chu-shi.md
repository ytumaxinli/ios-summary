#### 分类**用途**

> 1 已经存在的类添加方法（基础用途）
>
> 2 把类的实现分开在几个不同的文件里面
>
> > 这样做的好处如下：
> >
> > ```
> > 2.1可以减少单个文件的体积
> > 2.2可以把不同的功能组织到不同的category里
> > 2.3可以由多个开发者共同完成一个类
> > 2.4可以按需加载想要的category 等等
> > ```
>
> 3 模拟多继承
>
> 4 把framework的私有方法公开（hook 系统方法）

#### 基本使用

> **示例**
>
> ```
> @interface NSObject (Extends)
> - (void)instaceMethodTest;
> + (void)classMethodTest;
> @end
>
> @implementation NSObject (Extends)
>
> - (void)instaceMethodTest
> {
>     NSLog(@"instaceMethodTest");
> }
>
> + (void)classMethodTest
> {
>     NSLog(@"calssMethodTest");
> }
> @end
>
> NSObject *object = [[NSObject alloc] init];
> [object instaceMethodTest];
>         
> [NSObject classMethodTest];
>
> //打印结果
> [59791:6507547] instaceMethodTest
> [59791:6507547] calssMethodTest
> ```
>
> **分析**
>
> 由于我们知道类的实例方法存储在类对象中，类方法存储在元类对象中。因此可以确定，分类里声明和实现的实例方法和类方法，在某个时刻分别被复制/移动到了类对象和元类对象中。这个时刻大概有两种可能性
>
> > 1 编译的时候：在下一节Cagegory C++分析中会将，通过查看编译后的C++代码研究分类的结构。发现分类编译后会生成一个category\_t的结构体，分别包含了类方法列表、实例方法列表、协议列表和属性列表，因此可以确定category信息并没有在编译的时候合并到类对象和分类对象中。
> >
> > 2 运行的时候：在之后的一张Category 源码分析中，会看到在运行时初始化的时候会将分类信息中的实例方法列表、协议列表、属性列表复制/移动到类对象的class\_rw\_t中分别对应methods、properties、protocols属性。将类对象中的类方法信息移动至元类对象的class\_rw\_t中的methods。



