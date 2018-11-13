### WorkSpace多工程协同工作

#### **文章摘要**

> 1. **项目背景**
>
> 2. **Workspaces使用**
>
> 3. **Framework使用**
>
> 4. **建立依赖**
>
> 5. **参考资料**

* #### 项目背景

由于业务需求，需要对外开发一个framework提供给第三方使用。由于要同时提供framework及使用demo，因此采用workspace管理test project和framework project。

* #### **Workspaces使用**
* 创建存放workspace、project的文件夹。本文文件夹命名为MSDKSpace。

* 新建workspace（本文命名为MSDKProjects），存放在MSDKSpace文件夹中。操作流程Xcode--&gt;File--&gt;New--&gt;Workspace--&gt;MSDKProjects。

* 新建framework（本文命名为MSDK），也存放在MSDKSpace文件夹中。操作流程Xcode--&gt;File--&gt;New--&gt;Project--&gt;Cocoa Touch Framework--&gt;MSDK
* 新建project（本文命名为MSDKTest），同样存放在MSDKSpace文件夹中。操作流程Xcode--&gt;File--&gt;New--&gt;Project--&gt;Single View App--&gt;MSDKTest
* 打开MSDKProjects.xcworkspace，将MSDK.xcodeproj和MSDKTest.xcodeproj两个工程文件添加到MSDKProjects.xcworkspace。
* 新建podfile，MSDK与MSDKTest共用pod库。

```
workspace 'MSDKProjects.xcworkspace'   //workspace文件名
project 'MSDKTest/MSDKTest.xcodeproj'  //主工程路径

target 'MSDKTest'do
 project 'MSDKTest/MSDKTest.xcodeproj' //工程路径
 platform :ios, '8.0'
 source 'https://github.com/CocoaPods/Specs.git'
 pod 'AFNetworking','~>3.2.0'
end

target 'MSDK'do
 project 'MSDK/MSDK.xcodeproj'         //工程路径
 platform :ios, '8.0'
 source 'https://github.com/CocoaPods/Specs.git'
 pod 'Masonry'
end
```

* #### **Framework使用**
* 需要暴露的头文件从Headers中的Project目录下移动至Public目录下。操作流程Build Phases--&gt;Headers--&gt;文件移动至Public

* 添加的framework默认是动态库，需要将**framework**设置为静态库。操作流程Build Settings--&gt;Match-O Type--&gt;Static Library

* 在MSDK工程中创建MSDK.bundle，用于存放对外暴露的图片和配置文件等。
* 将图片、json等文件拖入MSDK.bundle。在framework工程中读取bundle中的文件和图片需要先获取bundle文件路径，如NSString \*path = \[\[NSBundle mainBundle\] pathForResource:@"LYApi.bundle" ofType:nil\]； \[UIImage imageNamed:name inBundle:\[NSBundle bundleWithPath:path\] compatibleWithTraitCollection:nil\]
* framework支持bitcode,操作流程Building Seting --&gt;  Custom Compiler Flags --&gt; 添加 -fembed-bitcode

* #### 建立依赖
* 向MSDKTest添加MSDK.framework依赖。操作流程，在MSDKTest--&gt;Targets--&gt;Build Phases--&gt;Link Binary with Libraries添加自定义的MSDK.framework

* 向MSDKTest添加MSDK的header头文件索引。操作流程，MSDKTest--&gt;Targets--&gt;Build Settings--&gt;User Header Search Paths--&gt;$\(SRCROOT\)/../MSDK（并设置递归搜索，recursive）

* 不能在 MSDKTest--&gt;Targets--&gt;General--&gt;Embedded Binaries添加自定义的MSDK.framework。否则在打release包时会报错，[Found an unexpected Mach-O header code](#)
* 注意两个工程的Deployment Target版本号，MSDK版本兼容MSDKTest版本否则会报MSDK.framework找不到。

* #### **参考资料**

[iOS建一个framework和frameworkTest项目在workspace中](https://www.jianshu.com/p/a488399a14cd)

[iOS用workspace和cocoapods管理多个项目](http://blog.sina.com.cn/s/blog_14ddfbc6f0102x8tx.html)

[学会使用cocoapods+workspace管理多个工程和组件化framework](https://www.jianshu.com/p/9eaebf93466f)

[关于 invalid bitcode signature](https://juejin.im/entry/5948c3b88d6d81cc72fd2c5e)

[Xcode: two Apps with a shared private Framework, in a WorkSpace](http://netsplit.com/xcode-two-apps-with-a-shared-private-framework-in-a-workspace)

[Found an unexpected Mach-O header code: 0x72613c21](https://www.jianshu.com/p/49f94e711fc2)

[Embedded Binaries 和 Linked Frameworks and Libraries的区别](https://www.googleapple.com/Embedded-Binaries-和-Linked-Frameworks-and-Libraries的区别.html)

