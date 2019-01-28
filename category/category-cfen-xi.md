#### **OC转C++命令行**

```
xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc 文件名.m [-o 导出文件名.cpp] //中括号内可选
```

#### **转化后的C++代码**

> ```
> struct _category_t {
>     const char *name;
>     struct _class_t *cls;
>     const struct _method_list_t *instance_methods;
>     const struct _method_list_t *class_methods;
>     const struct _protocol_list_t *protocols;
>     const struct _prop_list_t *properties;
> };
>
> //转化后的NSString+Extends分类结构
> static struct _category_t _OBJC_$_CATEGORY_NSObject_$_Extends __attribute__ ((used, section ("__DATA,__objc_const"))) = 
> {
>     "NSObject",
>     0, // &OBJC_CLASS_$_NSObject,
>     (const struct _method_list_t *)&_OBJC_$_CATEGORY_INSTANCE_METHODS_NSObject_$_Extends,
>     (const struct _method_list_t *)&_OBJC_$_CATEGORY_CLASS_METHODS_NSObject_$_Extends,
>     (const struct _protocol_list_t *)&_OBJC_CATEGORY_PROTOCOLS_$_NSObject_$_Extends,
>     (const struct _prop_list_t *)&_OBJC_$_PROP_LIST_NSObject_$_Extends,
> };
>
> //instance_methods
> static struct /*_method_list_t*/ {
>     unsigned int entsize;  // sizeof(struct _objc_method)
>     unsigned int method_count;
>     struct _objc_method method_list[1];
> } _OBJC_$_CATEGORY_INSTANCE_METHODS_NSObject_$_Extends __attribute__ ((used, section ("__DATA,__objc_const"))) = {
>     sizeof(_objc_method),
>     1,
>     {{(struct objc_selector *)"instaceMethodTest", "v16@0:8", (void *)_I_NSObject_Extends_instaceMethodTest}}
> };
>
> struct _objc_method {
>     struct objc_selector * _cmd;
>     const char *method_type;
>     void  *_imp;
> };
>
> //class_methods
> static struct /*_method_list_t*/ {
>     unsigned int entsize;  // sizeof(struct _objc_method)
>     unsigned int method_count;
>     struct _objc_method method_list[1];
> } _OBJC_$_CATEGORY_CLASS_METHODS_NSObject_$_Extends __attribute__ ((used, section ("__DATA,__objc_const"))) = {
>     sizeof(_objc_method),
>     1,
>     {{(struct objc_selector *)"classMethodTest", "v16@0:8", (void *)_C_NSObject_Extends_classMethodTest}}
> };
>
> //protocols
> static struct /*_protocol_list_t*/ {
>     long protocol_count;  // Note, this is 32/64 bit
>     struct _protocol_t *super_protocols[2];
> } _OBJC_CATEGORY_PROTOCOLS_$_NSObject_$_Extends __attribute__ ((used, section ("__DATA,__objc_const"))) = {
>     2,
>     &_OBJC_PROTOCOL_NSCopying,
>     &_OBJC_PROTOCOL_NSCoding
> };
>
> struct _protocol_t {
>     void * isa;  // NULL
>     const char *protocol_name;
>     const struct _protocol_list_t * protocol_list; // super protocols
>     const struct method_list_t *instance_methods;
>     const struct method_list_t *class_methods;
>     const struct method_list_t *optionalInstanceMethods;
>     const struct method_list_t *optionalClassMethods;
>     const struct _prop_list_t * properties;
>     const unsigned int size;  // sizeof(struct _protocol_t)
>     const unsigned int flags;  // = 0
>     const char ** extendedMethodTypes;
> };
>
> struct _protocol_t _OBJC_PROTOCOL_NSCopying __attribute__ ((used)) = {
>     0,
>     "NSCopying",
>     0,
>     (const struct method_list_t *)&_OBJC_PROTOCOL_INSTANCE_METHODS_NSCopying,
>     0,
>     0,
>     0,
>     0,
>     sizeof(_protocol_t),
>     0,
>     (const char **)&_OBJC_PROTOCOL_METHOD_TYPES_NSCopying
> };
>
> struct _protocol_t _OBJC_PROTOCOL_NSCoding __attribute__ ((used)) = {
>     0,
>     "NSCoding",
>     0,
>     (const struct method_list_t *)&_OBJC_PROTOCOL_INSTANCE_METHODS_NSCoding,
>     0,
>     0,
>     0,
>     0,
>     sizeof(_protocol_t),
>     0,
>     (const char **)&_OBJC_PROTOCOL_METHOD_TYPES_NSCoding
> };
>
> //properties
> static struct /*_prop_list_t*/ {
>     unsigned int entsize;  // sizeof(struct _prop_t)
>     unsigned int count_of_properties;
>     struct _prop_t prop_list[1];
> } _OBJC_$_PROP_LIST_NSObject_$_Extends __attribute__ ((used, section ("__DATA,__objc_const"))) = {
>     sizeof(_prop_t),
>     1,
>     {{"name","T@\"NSString\",N"}}
> };
>
> struct _prop_t {
>     const char *name;
>     const char *attributes;
> };
> ```

#### 源码中category结构声明

> ```
> struct category_t {
>     const char *name;                                         //结构体名称                               
>     classref_t cls;
>     struct method_list_t *instanceMethods;                    //实例方法列表
>     struct method_list_t *classMethods;                       //类方法列表 
>     struct protocol_list_t *protocols;
>     struct property_list_t *instanceProperties;               //属性列表
>     
>     // Fields below this point are not always present on disk.//以下部分不是
>     struct property_list_t *_classProperties;
>
>     method_list_t *methodsForMeta(bool isMeta) {
>         if (isMeta) return classMethods;
>         else return instanceMethods;
>     }
>
>     property_list_t *propertiesForMeta(bool isMeta, struct header_info *hi);
> };
> ```



