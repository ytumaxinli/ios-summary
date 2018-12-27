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

* **基础类型外部局部变量：**如 int i
* > 1. block结构体中，新增成员 int i;
  > 2. block构造方法中增加形参int i,构造方法将外部变量i的值传递给形参int i;
  > 3. block方法体内部不能修改外部变量int i的值；
* **对象类型外局布局变量：**如 UILable \*lable
* >





