库相关的操作主要是用于获取由系统提供的库相关的信息，主要包含以下函数：

```
// 获取所有加载的Objective-C框架和动态库的名称
const char ** objc_copyImageNames ( unsigned int *outCount );
 
// 获取指定类所在动态库
const char * class_getImageName ( Class cls );
 
// 获取指定库或框架中所有类的类名
const char ** objc_copyClassNamesForImage ( const char *image, unsigned int *outCount );
```

通过这几个函数，我们可以了解到某个类所有的库，以及某个库中包含哪些类。如下代码所示

```
unsigned int imageNamesCount;
unsigned int classNamesCount;
//获取工程所有的动态库
const char **imageNames = objc_copyImageNames(&imageNamesCount);
for (unsigned int imageNameIndex = 0; imageNameIndex < imageNamesCount; imageNameIndex++) {
    NSLog(@"class name: %s", imageNames[imageNameIndex]);
    //通过动态库中所有class
    const char ** classes = objc_copyClassNamesForImage(imageNames[imageNameIndex], &classNamesCount);
    for (unsigned int classNameIndex = 0; classNameIndex < classNamesCount; classNameIndex++) {
        NSLog(@"%s",classes[classNameIndex]);
    }
}

 
NSLog(@"获取指定类所在动态库");
NSLog(@"UIView's Framework: %s", class_getImageName(NSClassFromString(@"UIView")));
NSLog(@"获取指定库或框架中所有类的类名");

//获取UIView所在动态库中所有类的类名
const char ** classes = objc_copyClassNamesForImage(class_getImageName(NSClassFromString(@"UIView")), &outCount);
for (int i = 0; i < outCount; i++) {
    NSLog(@"class name: %s", classes[i]);
}

//其输出结果如下：
2014-11-08 12:57:32.689 [747:184013] 获取指定类所在动态库
2014-11-08 12:57:32.690 [747:184013] UIView's Framework: /System/Library/Frameworks/UIKit.framework/UIKit
2014-11-08 12:57:32.690 [747:184013] 获取指定库或框架中所有类的类名
2014-11-08 12:57:32.691 [747:184013] class name: UIKeyboardPredictiveSettings
2014-11-08 12:57:32.691 [747:184013] class name: _UIPickerViewTopFrame
2014-11-08 12:57:32.691 [747:184013] class name: _UIOnePartImageView
2014-11-08 12:57:32.692 [747:184013] class name: _UIPickerViewSelectionBar
2014-11-08 12:57:32.692 [747:184013] class name: _UIPickerWheelView
2014-11-08 12:57:32.692 [747:184013] class name: _UIPickerViewTestParameters
```

参考文献：

http://www.cocoachina.com/ios/20141111/10186.html

