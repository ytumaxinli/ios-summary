接下来就让我们对已经符号化以后的crash文件进行分析。  
**一、Crash文件结构**

网上已有的分类比较多，我这里直接把我目前一般找crash原因的模块展示出来，其他的就留待各位自己去研究了，分别是设备和crash信息、异常信息、线程信息  
 1、首先是设备和crash信息

```
ncident Identifier: F3573A...E2F244A               //crash的id
CrashReporter Key:   cc2298...es77eeb              //crash的设备id
Hardware Model:      iPhone7,2                     //手机型号
Process:             [AppName] [1816]              //APP的名字[进程的id]
Path:                /private/.../Application...   //APP的位置
Identifier:          com....                       //bundle ID
Version:             14 (2.3.5)                    //版本号
Code Type:           ARM-64 (Native)               //CPU类型
Parent Process:      launchd [1]

Date/Time:           2015-10-26 15:03:29.29 +0800    //crash发生时间
Launch Time:         2015-10-26 14:58:28.28 +0800    //进入应用时间
OS Version:          iOS 9.1 (13B143)                //iOS版本
Report Version:      105


Incident Identifier: 94CBAC98-A567-4AFB-A645-B6056211845B        //崩溃报告的唯一标识符
CrashReporter Key:   733a3d2e7316c1b1fb6bc050b38da6e5301da268    //设备标识相对应的唯一键值(并非真正的UDID)通常同一设备上同一版本的App发生Crash时，该值相同
Hardware Model:      iPhone8,2                                   //Crash的设备类型 
Process:             AutoLayout [22273]                          //代表Crash的进程名称，通常都是的App的名字, []里面是当时进程的ID
Path:                /private/var/.../AutoLayout.app/AutoLayout  //可执行程序在手机上的存储位置,注意XXX.app其实是作为一个Bundle的，真正的可执行文件是Bundle里面的XXX
Identifier:          com.xxx.www                                 //App的Indentifier，通常为“com.xxx.yyy”
Version:             1 (1.0)                                     //当前App的版本号，即Info.plist中CFBundleShortVersionString and CFBundleVersion
AppStoreTools:       10B61
Code Type:           ARM-64 (Native)                             //当前App的CPU架构
Role:                Non UI
Parent Process:      launchd [1]
Coalition:           com.xxx.www [1371]

Date/Time:           2019-01-09 18:30:40.5953 +0800             //crash发生时间
Launch Time:         2019-01-09 18:08:58.7742 +0800             //进入应用时间
OS Version:          iPhone OS 12.1.2 (16C101)                  //操作系统版本
Baseband Version:    5.32.00
Report Version:      104

```

当你有大量的crash文件的时候，你就可以对crash文件里面的 Hardware Model，Version ， OS Version等进行分类，就可以获知到很多信息，比如说，你会知道crash一般发生原因是因为手机型号，还是App版本，或者还是手机版本的原因。

2、其次是异常信息

```
Exception Type:  EXC_BAD_ACCESS (SIGABRT)                      //异常的类型
Exception Subtype: KERN_INVALID_ADDRESS at 0x0000000000000118  //异常子类型
Triggered by Thread:  0                    //异常发生的线程(0为主线程，其他为子线程)
```

3、线程信息

```
Last Exception Backtrace:
0   CoreFoundation                  0x182780f48 __exceptionPreprocess + 124
1   libobjc.A.dylib                 0x197333f80 objc_exception_throw + 56
2   CoreFoundation                  0x182780e90 +[NSException raise:format:] + 120
3   [AppName]                           0x100c42a40 UmengSignalHandler + 144
4   libsystem_platform.dylib        0x197d6193c _sigtramp + 52
5   [AppName]                           0x1005d9f38 CScopePtr<IAVGAudioLogic>::operator IAVGAudioLogic*<IAVGAudioLogic>() (xprefc.h:165)
6   [AppName]                           0x1005d3b8c tencent::av::AVRoomMultiImpl::GetAudioLogic() (av_room_multi_impl.h:119)
7   [AppName]                           0x10057076c tencent::av::AVAudioCtrlImpl::SetAudioOutputMode(int) (av_audio_ctrl_impl.cpp:443)
8   [AppName]                           0x10044dc3c -[AVBasicManager changeSpeakerMode:] (AVManager.mm:525)
9   [AppName]                           0x100296e1c -[KTQAVRoom enableSpeakerMode:] (KTQAVRoom.m:345)
10  [AppName]                           0x1002970d0 -[KTQAVRoom settingSpeaker:] (KTQAVRoom.m:362)
11  [AppName]                           0x1003d5464 -[KTChatView onAudioNotificationReceived:] (KTChatView.m:685)
```

**二、常见的Crash类型**

> **1、Watchdog timeout**
>
> > Exception Code：0x8badf00d， 不太直观，可以读成“eat bad food”，意思是don‘t block main thread
> >
> > 紧接着下面会有一段描述：
> >
> > ```
> > Application Specific Information：
> > com.xxx.yyy　　 failed to resume in time
> > ```
> >
> > 对于此类Crash，我们应该去审视自己App初始化时做的事情是否正确，是否在主线程请求了网络，或者其他耗时的事情卡住了正常初始化流程。
> >
> > 通常系统允许一个App从启动到可以相应用户事件的时间最多为5S，如果超过了5S，App就会被系统终止掉。在Launch，resume，suspend，quit时都会有相应的时间要求。在Highlight Thread里面我们可以看到被终止时调用到的位置，xxxAppDelegate加上行号。
> >
> > PS. 在连接Xcode调试时为了便于调试，系统会暂时禁用掉Watchdog，所以此类问题的发现需要使用正常的启动模式。
>
> **2、User force-quit**
>
> > ```
> > Exception Codes: 0xdeadfa11, deadfall
> > ```
> >
> > 这个强制退出跟我们平时所说的kill掉后台任务操作还不太一样，通常在程序bug造成系统无法响应时可以采用长按电源键，当屏幕出现关机确认画面时按下Home键即可关闭当前程序。
>
> **3、Low Memory termination**
>
> > 跟一般的Crash结构不太一样，通常有Free pages，Wired Pages，Purgeable pages，largest process 组成，同事会列出当前时刻系统运行所有进程的信息。
> >
> > 关于Memory warning可以参看我之前写的一篇文章IOS 内存警告[Memory warning level](http://www.cnblogs.com/smileEvday/archive/2012/03/07/MemoryWarning.html)。
> >
> > App在运行过程中，系统内存紧张时通常会先发警告，同时把后台挂起的程序终止掉，最终如果还是内存不够的话就会终止掉当前前台的进程。
> >
> > 当接受到内存警告的事后，我们应该释放尽可能多的内存，Crash其实也可以看做是对App的一种保护。
>
> **4、Crash due to bugs**
>
> > 因为程序bug导致的Crash通常千奇百怪，很难一概而论。大部分情况通过Crash日志就可以定位出问题，当然也不排除部分疑难杂症看半天都不值问题出在哪儿。这个就只能看功底了，一点点找，总是能发现蛛丝马迹。是在看不出来时还可以求助于Google大神，总有人遇到和你一样的Bug

**三、常见的Exception Type & Exception Code**

> **1、Exception Type**
>
> > 1\) EXC\_BAD\_ACCESS**:** 此类型的Excpetion是我们最长碰到的Crash，通常用于访问了不改访问的内存导致。一般EXC\_BAD\_ACCESS后面的"\(\)"还会带有补充信息。
> >
> > > **SIGABRT:**收到Abort信号退出，通常Foundation库中的容器为了保护状态正常会做一些检测，例如插入nil到数组中等会遇到此类错误。
> >
> > 2）EXC\_BAD\_INSTRUCTION
> >
> > 此类异常通常由于线程执行非法指令导致
> >
> > 3）EXC\_ARITHMETIC
> >
> > 除以零错误会抛出此类异常
>
> **2、Exception Code**
>
> > **0xbaaaaaad **此种类型的log意味着该Crash log并非一个真正的Crash，它仅仅只是包含了整个系统某一时刻的运行状态。通常可以通过同时按Home键和音量键，可能由于用户不小心触发
> >
> > **0xbad22222**当VOIP程序在后台太过频繁的激活时，系统可能会终止此类程序
> >
> > **0x8badf00d**这个前面已经介绍了，程序启动或者恢复时间过长被watch dog终止
> >
> > **0xc00010ff**程序执行大量耗费CPU和GPU的运算，导致设备过热，触发系统过热保护被系统终止
> >
> > **0xdead10cc**程序退到后台时还占用系统资源，如通讯录被系统终止
> >
> > **0xdeadfa11**前面也提到过，程序无响应用户强制关闭



