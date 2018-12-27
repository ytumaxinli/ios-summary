**OC代码转化为C++代码**

> **1.查看当前xcode正在使用的编译版本**
>
> ```
> //终端中输入xcodebuild -showsdks
> bogon:~ tsmc$ xcodebuild -showsdks
>
> //输出结果
> iOS SDKs:
>     iOS 12.1                          -sdk iphoneos12.1
>
> iOS Simulator SDKs:
>     Simulator - iOS 12.1              -sdk iphonesimulator12.1
> ```
>
> **2. 通过clang编译器将OC转化为C++代码命令**
>
> ```
> //ios
> xcrun -sdk iphonesimulator11.4 clang -S -rewrite-objc -fobjc-arc -fobjc-runtime=ios-11.4 文件名
>
> //mac
> xcrun -sdk macosx10.14 clang -S -rewrite-objc -fobjc-arc 文件名
> ```

**Block中使用外部变量结论总结**

> **基础类型外部局部变量：**如 int i
>
> > 1. block结构体新增成员 int i
> > 2. block构造方法中增加形参int i,构造方法将外部变量i的值传递给形参int i
> > 3. block方法体内部不能修改外部变量int i的值
>
> **对象类型外局局部变量：如 UILable \*lable**
>
> > 1. block结构体新增成员UILabel \*\_\_strong tmpLabel,对外部变量进行强引用；
> > 2. block构造方法中增加参数UILabel \*\_\_strong \_tmpLabel,构造方法将外部变量lable所指向的地址赋值给参数\_tmpLabel进行强引用
> > 3. 由于\_tmpLabel引用的是对象的地址，因此外部变量lable值改变时\_tmpLabel的值不改变
> > 4. block方法体内部不能修改外部变量UILable \*lable的值
>
> **\_\_weak修饰的局部外部变量：如\_\_weak UILabel \*label**
>
> > 1. block结构体新增成员UILabel \*\_\_weak tmpLabel,对外部变量进行弱引用；
> > 2. block构造方法中增加参数UILabel \*\_\_weak \_tmpLabel,构造方法将外部变量lable所指向的地址赋值给参数\_tmpLabel进行弱引用
> > 3. 由于结构体成员进行的是弱引用，因此可能使用时\_tmpLabel的值为nil;
> > 4. block方法体内部不能修改外部变量UILable \*lable的值
>
> **成员变量:**如 当前类名为ViewController,成员变量为UILable \*lable
>
> > 1. block结构体新增成员ViewController\*const \_\_strong self，**对当前类进行强引用**。如果当前类也强引用block的话就会造成循环引用。
> > 2. block构造方法中新增参数ViewController \*const \_\_strong \_self，构造方法将当前类对象self赋值给参数\_self进行强引用。
> > 3. 由于block方法体中使用的成员变量lable，是通过block结构体中的成员\_self进行获取，因此保证**block内部和外部的到的lable的值都是相同的**。
>
> **weakSelf.属性：**如 当前类名为ViewController,属性为UILable \*lable
>
> > 1. block结构体新增成员ViewController \*const \_\_weak wself，对当前类进行弱引用。即使当前类强引用block也不会出现循环引用。这就是\_\_weak typeof\(self\) weakSelf = self,作用的意义。
> > 2. block构造方法中添加ViewController \*const \_\_weak \_wself参数，构造方法将当前类的弱引用传赋值给了block结构体的成员wself
> > 3. 使用weakSelf的时候，我们通常会在block体内强引用weakSelf（\_\_strong typeof\(self\) strongSelf = weakSelf）的原因是：由于block结构体的成员弱引用的当前类，而在block体的逻辑代码也是通过弱引用的wself索引对应的属性变量。由于弱引用的原因导致在执行block体内逻辑代码的过程中wself可能会被释放，因此在逻辑代码开头进行强引用。由于block体中的逻辑代码被编译到了一个独立函数中，并不在block结构体中，因此在这里强引用self不会造成循环引用。
> > 4. block内外读取/设置属性效果相同。
>
> **\_\_block外部局部变量：**如 \_\_block int a = 100;
>
> > 1. \_\_block关键字编译后成为结构体\_\_Block\_byref\_a\_0。结构体重包含成员\_\_Block\_byref\_a\_0 \*\_\_forwarding;
> > 2. block结构体新增成员\_\_Block\_byref\_a\_0 \*a, 构造方法将赋值成员\_\_Block\_byref\_a\_0为地址\(\_\_Block\_byref\_a\_0 \*\)&a，falgs=570425344。
> > 3. block方法体内逻辑代码使用的是a-&gt;\_\_forwarding-&gt;a。
>
> **外部全局变量：**如 int globalValue = 10
>
> > 1. 全局变量编译后仍为全局变量
> > 2. block结构体中没有新增成员
> > 3. block方法体内逻辑代码直接使用全局变量globalValue
> > 4. block内外读取/设置全部变量效果相同
>
> **全局静态变量：**如 static int staticValue = 5
>
> > 1. 全局静态变量编译后仍为全局静态变量
> > 2. block结构体中没有新增成员
> > 3. block方法体内逻辑代码直接使用全局静态变量globalValue
> > 4. block内外读取/设置全局静态变量效果相同
>
> **局部静态变量： 如**static int a
>
> > 1. 局部静态变量编译后仍为局部静态变量
> > 2. block结构体中新增成员指针a\(int \*a\),构造方法中将局部静态变量a的地址赋\(&a\)值给结构体成员int \*a
> > 3. block方法体内逻辑代码使用指针a\(int \*a\),操作a的值
> > 4. 由于操作地址，block内外读取/设置局部静态变量的效果相同



