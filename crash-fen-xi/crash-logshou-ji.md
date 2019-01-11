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



