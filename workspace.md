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
> 5. **相关配置信息**
>
> 6. **参考资料**

#### 

* #### 项目背景

由于业务需求，需要对外开发一个framework提供给第三方使用。由于要同时提供framework及使用demo，因此采用workspace管理测试project和framework project。

* #### **Workspaces使用**

1. 创建存放workspace、project的文件夹。本文文件夹命名为MSDKSpace。

2. 新建workspace（本文命名为MSDKProjects），存放在MSDKSpace文件夹中。操作流程Xcode--&gt;File--&gt;New--&gt;Workspace--&gt;MSDKProjects。

3. 新建framework（本文命名为MSDK），也存放在MSDKSpace文件夹中。操作流程Xcode--&gt;File--&gt;New--&gt;Project--&gt;Cocoa Touch Framework--&gt;MSDK

4. 新建project（本文命名为MSDKTest），同样存放在MSDKSpace文件夹中。操作流程Xcode--&gt;File--&gt;New--&gt;Project--&gt;Single View App--&gt;MSDKTest

5. 打开MSDKProjects.xcworkspace，将MSDK.xcodeproj和MSDKTest.xcodeproj两个工程文件添加到MSDKProjects.xcworkspace。

* #### **Framework使用**

1. 在MSDK工程中创建MSDK.bundle，用于存放对外暴露的图片和配置文件等。
2. 将图片、json等文件拖入MSDK.bundle。在



参考资料

[iOS建一个framework和frameworkTest项目在workspace中](https://www.jianshu.com/p/a488399a14cd)

[关于 invalid bitcode signature](https://juejin.im/entry/5948c3b88d6d81cc72fd2c5e)

[Xcode: two Apps with a shared private Framework, in a WorkSpace](http://netsplit.com/xcode-two-apps-with-a-shared-private-framework-in-a-workspace)

[Found an unexpected Mach-O header code: 0x72613c21](https://www.jianshu.com/p/49f94e711fc2)

[Embedded Binaries 和 Linked Frameworks and Libraries的区别](https://www.googleapple.com/Embedded-Binaries-和-Linked-Frameworks-and-Libraries的区别.html)



