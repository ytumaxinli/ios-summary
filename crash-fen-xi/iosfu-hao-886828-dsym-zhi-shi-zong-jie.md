#### **将可执行文件的16进制地址转化成文件名、函数名、行号的过程称之为Symbolication（符号化）**



#### 什么是符号表\(**dsym**\)

> iOS构建时产生的符号表，它是内存地址与文件名、函数名、行号的映射表。 符号表元素如下所示:
>
> ```
> <起始地址> <结束地址> <函数> [<文件名:行号>]
> ```

#### 符号表\(**dsym\)作用**

> 当release的版本应用 crash的时候,会在手机上自动生成一个crash log文件。这个crash log包含了出错的内存地址，我们可以利用crash时的堆栈信息和dSYM符号表得到对应到源代码的堆栈信息以及出错的代码行数，因此能快速定位出错的代码位置，以便快速解决问题。

#### 符号化

> **解析Crash需要具备的三个文件**
>
> > 1.Xcode自带的崩溃分析工具symbolicatecrash。使用这个工具可以更精确的定位崩溃所在的位置，将0x开头的地址替换为响应的代码和具体行数。
> >
> > ```
> > //获取symbolicatecrash工具
> >
> > 打开终端输入以下命令：find /Applications/Xcode.app -name symbolicatecrash -type f
> >
> > bogon:~ tsmc$ find /Applications/Xcode.app -name symbolicatecrash -type f
> > /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/Library/PrivateFrameworks/DVTFoundation.framework/symbolicatecrash
> > /Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/symbolicatecrash
> > ```
> >
> > 2.我们打包时产生的xx.app.dSYM文件。
> >
> > 3.崩溃时产生的Crash文件,例如：xx.crash。
>
> **检查UUID一致性**
>
> > 每一个xx.dSYM 文件和 xx.crash 文件都有自己的 UUID，只要这两个文件的 UUID 一致，我们才可以通过他们解析出正确的错误函数信息了。\(xx代表你的项目名\)
> >
> > 1.查看 xx.app.dSYM 文件的 UUID ，在 terminal 中输入命令：
> >
> > ```
> > dwarfdump --uuid xx.app.dSYM
> > ```
> >
> > 2.crash 文件内 Binary Images: 下面一行中 &lt;&gt;内的 3b2463066e3736d3a73fbe8f1ad652da 就是该 crash 文件的 UUID，而第一个地址 0x1047e0000 便是 slide address:
> >
> > ```
> > Binary Images:
> > 0x1047e0000 - 0x1047f3fff AutoLayout arm64  <3b2463066e3736d3a73fbe8f1ad652da> /var/containers/Bundle/Application/25EF8451-297B-48BB-BFD0-6FD928B8123B/AutoLayout.app/AutoLayout
> > ```
>
> **符号化操作**
>
> > 1.将以上三个文件放入同一文件夹下，使用终端cd到该文件夹，输入命令符号化xx.crash文件:
> >
> > ```
> > ./symbolicatecrash xx.crash xx.app.dSYM > yy.crash
> > ```
> >
> > 2.如果报类似 Error: "DEVELOPER\_DIR" is not defined at ./symbolicatecrash line 69. 的错误，需要执行命令
> >
> > 检查一下环境变量:
> >
> > ```
> > xcode-select -print-path
> > ```
> >
> > 返回结果：
> >
> > ```
> > /Applications/Xcode.app/Contents/Developer
> > ```
> >
> > 如果不是上面的结果，需要使用下面命令设置一下导出的环境变量:
> >
> > ```
> > export DEVELOPER_DIR=/Applications/XCode.app/Contents/Developer
> > ```
> >
> > 3.设置环境变量成功后，重新符号化xx.crash得到符号化后的yy.crash文件
> > ```
> > ./symbolicatecrash xx.crash xx.app.dSYM > yy.crash
> > ```



