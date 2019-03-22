## 一、crash log的获取

当app 在手机上crash的时候，会在手机上自动生成一个崩溃日志，也就是我们说的Crash Log。

### 1、获取用户的 crash log

通过Xcode -&gt; Window -&gt;Organizer -&gt; Crashes，来查看App Store已上线的App相应版本的Crash Log（此功能之前在itunesConnect中）。这里可以查看解析后的Crash Log日志，也可以下载 .xccrashpoint 文件，通过显示包内容可以查看.crash文件。

这种方式有个前提，就是用户设备同意共享iPhone分析。设置-&gt;隐私-&gt;分析-&gt;共享iPhone分析

### 2、获取测试机的crash log

通过Xcode获取到崩溃日志，方法是Xcode-&gt;Window-&gt;Devices and Simulators -&gt; View Device Logs,来查看测试机的Crash Log。

### 3.友盟、bugly、Crashlytics，Flurry等

第三方可已经dSYM符号表文件上传至平台符号表管理，由第三方平台进行解析。

**获取崩溃信息**

在iOS中获取崩溃信息的方式有很多，比较常见的是使用友盟、百度等第三方分析工具，或者自己收集崩溃信息并上传公司服务器。下面列举一些我们常用的崩溃分析方式：

* 使用友盟、百度等第三方崩溃统计工具。

* 自己实现应用内崩溃收集，并上传服务器。

* Xcode-Devices中直接查看某个设备的崩溃信息。

* 使用苹果提供的Crash崩溃收集服务。

## Crash Log符号化

## Crash Log的分析

**收集崩溃信息**

苹果给我们提供了异常处理的类，NSException类。这个类可以创建一个异常对象，也可以通过这个类获取一个异常对象。

这个类中我们最常用的还是一个获取崩溃信息的C函数，我们可以通过这个函数在程序发生异常的时候收集这个异常。

```
// 将系统提供的获取崩溃信息函数写在这个方法中，以保证在程序开始运行就具有获取崩溃信息的功能
  - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
     // 将下面C函数的函数地址当做参数
     NSSetUncaughtExceptionHandler(&UncaughtExceptionHandler);
     return YES;
  }
  // 设置一个C函数，用来接收崩溃信息
  void UncaughtExceptionHandler(NSException *exception){
      // 可以通过exception对象获取一些崩溃信息，我们就是通过这些崩溃信息来进行解析的，例如下面的symbols数组就是我们的崩溃堆栈。
      NSArray *symbols = [exception callStackSymbols];
      NSString *reason = [exception reason];
      NSString *name = [exception name];
  }
```

## 



