**类别的用途**

> 1.已经存在的类添加方法（基础用途）
>
> 2.把类的实现分开在几个不同的文件里面
>
> ```
> 2.1可以减少单个文件的体积
>
> 2.2可以把不同的功能组织到不同的category里
>
> 2.3可以由多个开发者共同完成一个类
>
> 2.4可以按需加载想要的category 等等
> ```
>
> 3.模拟多继承
>
> 4.把framework的私有方法公开

类别的本质

> **类别的示例代码**
>
> > ```
> > @interface NSObject (Extends)<NSCopying, NSCoding>
> >
> > @property(nonatomic)NSString *name;
> >
> > @end
> >
> > @implementation NSObject (Extends)
> >
> > - (void)instaceMethodTest
> > {
> >     NSLog(@"instaceMethodTest");
> > }
> >
> > + (void)classMethodTest
> > {
> >     NSLog(@"calssMethodTest");
> > }
> >
> > @end
> > ```
>
> **转化后的C++代码**
>
> > ```
> > //类别结构体
> > struct _category_t {
> > 	const char *name;
> > 	struct _class_t *cls;
> > 	const struct _method_list_t *instance_methods;
> > 	const struct _method_list_t *class_methods;
> > 	const struct _protocol_list_t *protocols;
> > 	const struct _prop_list_t *properties;
> > };
> >
> > //转化后的NSString+Extends分类结构
> > static struct _category_t _OBJC_$_CATEGORY_NSObject_$_Extends __attribute__ ((used, section ("__DATA,__objc_const"))) = 
> > {
> > 	"NSObject",
> > 	0, // &OBJC_CLASS_$_NSObject,
> > 	(const struct _method_list_t *)&_OBJC_$_CATEGORY_INSTANCE_METHODS_NSObject_$_Extends,
> > 	(const struct _method_list_t *)&_OBJC_$_CATEGORY_CLASS_METHODS_NSObject_$_Extends,
> > 	(const struct _protocol_list_t *)&_OBJC_CATEGORY_PROTOCOLS_$_NSObject_$_Extends,
> > 	(const struct _prop_list_t *)&_OBJC_$_PROP_LIST_NSObject_$_Extends,
> > };
> >
> > //instance_methods
> > static struct /*_method_list_t*/ {
> > 	unsigned int entsize;  // sizeof(struct _objc_method)
> > 	unsigned int method_count;
> > 	struct _objc_method method_list[1];
> > } _OBJC_$_CATEGORY_INSTANCE_METHODS_NSObject_$_Extends __attribute__ ((used, section ("__DATA,__objc_const"))) = {
> > 	sizeof(_objc_method),
> > 	1,
> > 	{{(struct objc_selector *)"instaceMethodTest", "v16@0:8", (void *)_I_NSObject_Extends_instaceMethodTest}}
> > };
> >
> > //class_methods
> > static struct /*_method_list_t*/ {
> > 	unsigned int entsize;  // sizeof(struct _objc_method)
> > 	unsigned int method_count;
> > 	struct _objc_method method_list[1];
> > } _OBJC_$_CATEGORY_CLASS_METHODS_NSObject_$_Extends __attribute__ ((used, section ("__DATA,__objc_const"))) = {
> > 	sizeof(_objc_method),
> > 	1,
> > 	{{(struct objc_selector *)"classMethodTest", "v16@0:8", (void *)_C_NSObject_Extends_classMethodTest}}
> > };
> >
> > //protocols
> > static struct /*_protocol_list_t*/ {
> > 	long protocol_count;  // Note, this is 32/64 bit
> > 	struct _protocol_t *super_protocols[2];
> > } _OBJC_CATEGORY_PROTOCOLS_$_NSObject_$_Extends __attribute__ ((used, section ("__DATA,__objc_const"))) = {
> > 	2,
> > 	&_OBJC_PROTOCOL_NSCopying,
> > 	&_OBJC_PROTOCOL_NSCoding
> > };
> >
> > //properties
> > static struct /*_prop_list_t*/ {
> > 	unsigned int entsize;  // sizeof(struct _prop_t)
> > 	unsigned int count_of_properties;
> > 	struct _prop_t prop_list[1];
> > } _OBJC_$_PROP_LIST_NSObject_$_Extends __attribute__ ((used, section ("__DATA,__objc_const"))) = {
> > 	sizeof(_prop_t),
> > 	1,
> > 	{{"name","T@\"NSString\",N"}}
> > };
> > ```



