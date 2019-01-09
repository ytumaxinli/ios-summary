#### 符号化

> **解析Crash需要具备的三个文件**
>
> 1.Xcode自带的崩溃分析工具symbolicatecrash。使用这个工具可以更精确的定位崩溃所在的位置，将0x开头的地址替换为响应的代码和具体行数。
>
> ```
> //获取symbolicatecrash工具
>
> 打开终端输入以下命令：find /Applications/Xcode.app -name symbolicatecrash -type f
>
> bogon:~ tsmc$ find /Applications/Xcode.app -name symbolicatecrash -type f
> /Applications/Xcode.app/Contents/Developer/Platforms/WatchSimulator.platform/Developer/Library/PrivateFrameworks/DVTFoundation.framework/symbolicatecrash
> /Applications/Xcode.app/Contents/Developer/Platforms/AppleTVSimulator.platform/Developer/Library/PrivateFrameworks/DVTFoundation.framework/symbolicatecrash
> /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/Library/PrivateFrameworks/DVTFoundation.framework/symbolicatecrash
> /Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/symbolicatecrash
> ```
>
> 2.我们打包时产生的xx.app.dSYM文件。
>
> 3.崩溃时产生的Crash文件,例如：xx.crash。
>
> **检查UUID一致性 **\(xx代表你的项目名\)
>
> 每一个 xx.app 和 xx.app.dSYM 文件都有对应的 UUID，crash 文件也有自己的 UUID，只要这三个文件的 UUID 一致，我们就可以通过他们解析出正确的错误函数信息了。
>
> 1.查看 xx.app 文件的 UUID，terminal 中输入命令 ：`dwarfdump --uuid xx.app/xx`
>
> 2.查看 xx.app.dSYM 文件的 UUID ，在 terminal 中输入命令：`dwarfdump --uuid xx.app.dSYM`
>
> 3.3..crash 文件内 Binary Images: 下面一行中 &lt;&gt;内的 e86bcc8875b230279c962186b80b466d 就是该 crash 文件的 UUID，而第一个地址 0x1000ac000 便是 slide address:
>
> ```
> Binary Images:
>            0x1000ac000 - 0x100c13fff Example arm64 <e86bcc8875b230279c962186b80b466d> /var/containers/Bundle/Application/99EE
> ```
>
> **符号化操作**
>
> 打开终端，cd到当前crash文件夹，输入命令: `./symbolicatecrash xx.crash xx.app.dSYM > xx.crash`** （xx为文件名）**  
> 如果报类似 Error: "DEVELOPER\_DIR" is not defined at ./symbolicatecrash line 69. 的错误，需要执行命令  
> ** **`export DEVELOPER_DIR="/Applications/XCode.app/Contents/Developer"`  
>  然后重新 输入命令`./symbolicatecrash xx.crash xx.app.dSYM > xx.crash`这样就看到一个名字jieguo.crash 已经符号化的文件了



