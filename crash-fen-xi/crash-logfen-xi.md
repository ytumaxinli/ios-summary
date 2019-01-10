#### 一、**Crash文件结构**

1.Process Information\(进程信息\)

```
Incident Identifier: 94CBAC98-A567-4AFB-A645-B6056211845B        //崩溃报告的唯一标识符
CrashReporter Key:   733a3d2e7316c1b1fb6bc050b38da6e5301da268    //设备标识相对应的唯一键值(并非真正的UDID)通常同一设备上同一版本的App发生Crash时，该值相同
Hardware Model:      iPhone8,2                                   //Crash的设备类型 
Process:             AutoLayout [22273]                          //代表Crash的进程名称，通常都是的App的名字, []里面是当时进程的ID
Path:                /private/var/.../AutoLayout.app/AutoLayout  //可执行程序在手机上的存储位置,注意XXX.app其实是作为一个Bundle的，真正的可执行文件是Bundle里面的XXX
Identifier:          com.xxx.www                                 //App的Indentifier，通常为“com.xxx.yyy”
Version:             1 (1.0)                                     //当前App的版本号，即Info.plist中CFBundleShortVersionString and CFBundleVersion
AppStoreTools:       10B61
Code Type:           ARM-64 (Native)                             //当前App的CPU架构
```

2.Basic Information（基本信息）

```
Date/Time: 2019-01-09 18:30:40.5953 +0800 //crash发生时间
Launch Time: 2019-01-09 18:08:58.7742 +0800 //进入应用时间
OS Version: iPhone OS 12.1.2 (16C101) //操作系统版本
Baseband Version: 5.32.00
Report Version: 104
```

3.Exception（非常重要）

```
Exception Type:  EXC_CRASH (SIGABRT)                                 //异常类型
Exception Codes: 0x0000000000000000, 0x0000000000000000              //异常子类型
Exception Note:  EXC_CORPSE_NOTIFY
Triggered by Thread:  0                                              //发生异常的线程号

Last Exception Backtrace:
0   CoreFoundation                    0x20f574ec4 __exceptionPreprocess + 228
1   libobjc.A.dylib                   0x20e745a40 objc_exception_throw + 55
2   CoreFoundation                    0x20f4ec494 _CFThrowFormattedException + 111
3   CoreFoundation                    0x20f45c678 -[__NSArrayM objectAtIndex:] + 187
4   AutoLayout                        0x1047e7268 _hidden#4_ + 29288 (__hidden#15_:59)
5   UIKitCore                         0x23c9184b8 -[UITableView _selectRowAtIndexPath:animated:scrollPosition:notifyDelegate:] + 1347
...
15  UIKitCore                         0x23c70ad40 UIApplicationMain + 211
16  AutoLayout                        0x1047e7494 main + 29844 (__hidden#18_:14)
17  libdyld.dylib                     0x20efbebb4 start + 34.
```

4.线程回溯:  线程的回溯日志

```
Thread 0 name:  Dispatch queue: com.apple.main-thread
Thread 0 Crashed:
0   libsystem_kernel.dylib            0x000000020f10b104 __pthread_kill + 8
1   libsystem_pthread.dylib           0x000000020f187020 pthread_kill$VARIANT$mp + 380
2   libsystem_c.dylib                 0x000000020f062d78 abort + 140
...
12  AutoLayout                        0x00000001047e7494 main + 29844 (__hidden#18_:14)
13  libdyld.dylib                     0x000000020efbebb4 start + 4
```

5.线程状态: 闪退时寄存器中的值

```
thread 0 crashed with ARM Thread State (64-bit):
    x0: 0x0000000000000000   x1: 0x0000000000000000   x2: 0x0000000000000000   x3: 0x0000000283388637
    x4: 0x000000020e73cb81   x5: 0x000000016b61f4b0   x6: 0x000000000000006e   x7: 0x0000000060814842
    ...
    x28: 0x000000016b61fb10   fp: 0x000000016b61f410   lr: 0x000000020f187020
    sp: 0x000000016b61f3e0   pc: 0x000000020f10b104 cpsr: 0x00000000
```

6.二进制映象: 列出了闪退时已经加载的二进制文件。

```
Binary Images:
0x1047e0000 - 0x1047f3fff AutoLayout arm64  <3b2463066e3736d3a73fbe8f1ad652da> /var/containers/Bundle/Application/25EF8451-297B-48BB-BFD0-6FD928B8123B/AutoLayout.app/AutoLayout
0x1048ec000 - 0x1048f7fff libobjc-trampolines.dylib arm64  <2333d24ae8953e6199e1594fdf3bb8ab> /usr/lib/libobjc-trampolines.dylib
0x1049a4000 - 0x104a07fff dyld arm64  <30ff59036c17348cb2f0bb93dbc8f07a> /usr/lib/dyld
0x20e6ce000 - 0x20e6cffff libSystem.B.dylib arm64  <cb8c1b2d743f35a6ab42d1ba08356fd1> /usr/lib/libSystem.B.dylib
```

#### 二、两种主要产生崩溃日志的情况:

> **1.应用违反操作系统规则**
>
> > 违反iOS规则包括在启动、恢复、挂起、退出时watchdog超时、用户强制退出和低内存终止。让我们详细了解一下吧。
> >
> > **1.1 Watchdog 超时机制**
> >
> > > 从iOS 4.x开始，退出应用时并不会立即终止，而是退到后台。但是，如果你的应用响应不够快，操作系统有可能会终止你的应用，并产生一个崩溃日志。产生这种情况的主要原因是将耗时较长的操作\(如网络访问）放在后台线程上。
> > >
> > > Exception Code：0x8badf00d， 不太直观，可以读成“eat bad food”，意思是don‘t block main thread
> >
> > **1.2 用户强制退出 （待确认）**
> >
> > > 这个强制退出跟我们平时所说的kill掉后台任务操作还不太一样，通常在程序bug造成系统无法响应时可以采用长按电源键，当屏幕出现关机确认画面时按下Home键即可关闭当前程序。
> > >
> > > Exception Codes: 0xdeadfa11, deadfall
> >
> > **1.3低内存终止**
> >
> > > 跟一般的Crash结构不太一样，通常有Free pages，Wired Pages，Purgeable pages，largest process 组成.
>
>
>
> **2.应用中有Bug**
>
> > **1、Exception Type**
> >
> > > 1\) EXC\_BAD\_ACCESS**:** 此类型的Excpetion是我们最长碰到的Crash，通常用于访问了不改访问的内存导致。一般EXC\_BAD\_ACCESS后面的"\(\)"还会带有补充信息。
> > >
> > > > **SIGABRT:**收到Abort信号退出，通常Foundation库中的容器为了保护状态正常会做一些检测，例如插入nil到数组中等会遇到此类错误。
> > >
> > > 2）EXC\_BAD\_INSTRUCTION
> > >
> > > 此类异常通常由于线程执行非法指令导致
> > >
> > > 3）EXC\_ARITHMETIC
> > >
> > > 除以零错误会抛出此类异常
> >
> > **2、Exception Code**
> >
> > > **0xbaaaaaad **此种类型的log意味着该Crash log并非一个真正的Crash，它仅仅只是包含了整个系统某一时刻的运行状态。通常可以通过同时按Home键和音量键，可能由于用户不小心触发
> > >
> > > **0xbad22222**当VOIP程序在后台太过频繁的激活时，系统可能会终止此类程序
> > >
> > > **0x8badf00d**这个前面已经介绍了，程序启动或者恢复时间过长被watch dog终止
> > >
> > > **0xc00010ff**程序执行大量耗费CPU和GPU的运算，导致设备过热，触发系统过热保护被系统终止
> > >
> > > **0xdead10cc**程序退到后台时还占用系统资源，如通讯录被系统终止
> > >
> > > **0xdeadfa11**前面也提到过，程序无响应用户强制关闭





