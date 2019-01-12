当编译器遇到属性声明时（参见[Declared Properties](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjectiveC/Chapters/ocProperties.html#//apple_ref/doc/uid/TP30001163-CH17) in [The Objective-C Programming Language](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html#//apple_ref/doc/uid/TP30001163)），它会生成与封闭类，类别或协议相关联的描述性metadata。你可以使用支持按名称查找类或协议上的属性、以@encode字符串形式获取属性类型以及将属性的属性列表复制为C字符串数组的函数来访问此metadata。每个类和协议都有一个声明的属性列表。

#### Property Type and Functions

> Property结构定义了属性描述符的不透明句柄。
>
> ```
> typedef struct objc_property *Property;
> ```
>
> 你可以使用这些函数class\_copyPropertyList和protocol\_copyPropertyList分别检索与类关联的属性数组（包括已加载的类别）和协议：
>
> ```
> objc_property_t *class_copyPropertyList(Class cls, unsigned int *outCount)
> objc_property_t *protocol_copyPropertyList(Protocol *proto, unsigned int *outCount)
> ```
>
> 例如，给定以下类声明：
>
> ```
> @interface Lender : NSObject {
>     float alone;
> }
> @property float alone;
> @end
> ```
>
> 您可以使用以下方式获取属性列表：
>
> ```
> id LenderClass = objc_getClass("Lender");
> unsigned int outCount;
> objc_property_t *properties = class_copyPropertyList(LenderClass, &outCount);
> ```
>
> 您可以使用property\_getName函数来发现属性的名称：
>
> ```
> const char *property_getName(objc_property_t property)
> ```
>
> 您可以使用函数class\_getProperty和protocol\_getProperty分别获取对类和协议中具有给定名称的属性的引用：
>
> ```
> objc_property_t class_getProperty(Class cls, const char *name)
> objc_property_t protocol_getProperty(Protocol *proto, const char *name, BOOL isRequiredProperty, BOOL isInstanceProperty)
> ```
>
> 您可以使用property\_getAttributes函数来发现属性的名称和@encode类型字符串。 有关编码类型字符串的详细信息，请参阅[Type Encodings](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html#//apple_ref/doc/uid/TP40008048-CH100-SW1); 有关此字符串的详细信息，请参阅[Property Type String](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtPropertyIntrospection.html#//apple_ref/doc/uid/TP40008048-CH101-SW6)和。[Property Attribute Description Examples](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtPropertyIntrospection.html#//apple_ref/doc/uid/TP40008048-CH101-SW5).
>
> ```
> const char *property_getAttributes(objc_property_t property)
> ```
>
> 将这些放在一起，您可以使用以下代码打印与类关联的所有属性的列表：
>
> ```
> id LenderClass = objc_getClass("Lender");
> unsigned int outCount, i;
> objc_property_t *properties = class_copyPropertyList(LenderClass, &outCount);
> for (i = 0; i < outCount; i++) {
>     objc_property_t property = properties[i];
>     fprintf(stdout, "%s %s\n", property_getName(property), property_getAttributes(property));
> }
> ```

#### Property Type String

> 您可以使用property\_getAttributes函数来发现名称，属性的@encode类型字符串以及属性的其他属性。
>
> 该字符串以T开头，后跟@encode类型和逗号，并以V结尾，后跟后备实例变量的名称。 在这些属性之间，属性由以下描述符指定，以逗号分隔：![](/assets/屏幕快照 2019-01-12 下午3.22.44.png)

#### Property Attribute Description Examples

> 例如有这些定义：
>
> ```
> enum FooManChu { FOO, MAN, CHU };
> struct YorkshireTeaStruct { int pot; char lady; };
> typedef struct YorkshireTeaStruct YorkshireTeaStructType;
> union MoneyUnion { float alone; double down; };
> ```
>
> 下表显示了示例属性声明以及property\_getAttributes返回的相应字符串：![](/assets/屏幕快照 2019-01-12 下午3.30.29.png)

#### 

#### 



