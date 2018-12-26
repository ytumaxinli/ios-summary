**将OC代码转化为C++代码**

> **1.查看当前xcode正在使用的编译版本**
>
> ```
> bogon:~ tsmc$ xcodebuild -showsdks
>
> iOS SDKs:
> 	iOS 12.1                      	-sdk iphoneos12.1
>
> iOS Simulator SDKs:
> 	Simulator - iOS 12.1          	-sdk iphonesimulator12.1
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





