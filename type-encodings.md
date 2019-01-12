类型编码\(Type Encoding\)

为了协助Runtime，编译器将每个方法返回值和参数类型编码为一个字符串，并将字符串与selector相关联。这套编码也适用于其他场景，所以这个@encode编译器指令是设计成公开的。当给定一个类型时，@encode返回这个类型编码后的字符串。这些类型可以是诸如int、指针这样的基本类型，也可以是结构体、类等类型。事实上，任何可以作为sizeof\(\)操作参数的类型都可以用于@encode\(\)。

```
char *buf1 = @encode(int **);
char *buf2 = @encode(struct key);
char *buf3 = @encode(Rectangle);
```

下表列出了类型代码。注意，当编码一个对象用于归档或分配时，有许多代码是重叠的。然而，在编写编码器时，这里有些代码不能使用。也有些代码可以使用，但不是@encode\(\)所产生的。（关于编码对象的归档和分配的更多信息，可见基础框架引用中NSCode类规范。）![](/assets/屏幕快照 2019-01-12 下午3.49.27.png)

重要说明：Objective-C不支持long double类型。 @encode（long double）返回d，这与double的编码相同

数组的类型代码用方括号括起来; 数组中元素的数量是在数组类型之前的开括号之后立即指定的。 如下示例

```
float a[] = {1.0, 2.0, 3.0};
NSLog(@"array encoding type: %s", @encode(typeof(a)));

输出： array encoding type: [3f]
```

structures的类型代码用大括号，括号内首先列出structure tag（struct的结构名称）然后是等号，接着按顺序列出struct内的字段类型。比如

```
typedef struct example {
    id   anObject;
    char *aString;
    int  anInt;
} Example;
```

将编码后{example=@\*i}，如果是一个结构体的指针，那么encode为 ^{example=@\*i} ,如果是一个结构体的指针的指针，会删除内部类型则是 ^^{example}。

对象被视为结构。 例如，将NSObject类名传递给@encode（）会产生以下编码：{NSObject=\#}。NSObject类只声明了一个类类型的实例变量isa。

请注意，尽管@encode（）指令不返回它们，但运行时系统使用表6-2中列出的其他编码来表示类型限定符，当它们用于在协议中声明方法时。

![](/assets/屏幕快照 2019-01-12 下午3.46.57.png)

参考资料

> [https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html\#//apple\_ref/doc/uid/TP40008048-CH100-SW1](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html#//apple_ref/doc/uid/TP40008048-CH100-SW1)



