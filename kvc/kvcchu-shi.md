#### 什么是KVC

> KVC\(key value coding\)键值编码是一种可以使用字符串形式来间接操作对象相关属性的方法。OC在实现KVC时没有采用实现接口的方式，而是采用非正式协议即创建一个NSObject\(NSKeyValueCoding\) 类别，通过这样的方式使得NSObject的子类可以自行实现NSKeyValueCoding类别定义的相关方法。
>
> KVC使用非常简单，但KVC却异常强大，最暗黑的功能就是它**可以无视访问限制**，无论是否为private都可以进行赋值或取值操作，readonly的属性也可以无视，提供了一种比runtime更便捷的方式来修改或访问系统级隐藏的属性，因此，经常在**开发中通过runtime获取相关属性名后使用KVC来修改那些只读readonly或隐藏的属性**。

#### KVC主要方法

> ```
> @interface NSObject(NSKeyValueCoding)
>
>
> - (nullable id)valueForKey:(NSString *)key;                         //获取属性名为key的属性的值
> - (void)setValue:(nullable id)value forKey:(NSString *)key;         //设置属性名为key的属性的值为value
>
> - (nullable id)valueForKeyPath:(NSString *)keyPath;                 //获取嵌套属性属性的值
> - (void)setValue:(nullable id)value forKeyPath:(NSString *)keyPath; //设置嵌套属性的属性的值为value
>
>
> //获取属性名为key的属性值时，如果属性不存在则执行该方法。可自定义实现，默认实现方式为抛出NSUnknownKeyException异常
> - (nullable id)valueForUndefinedKey:(NSString *)key;
> //设置属性名为key的属性值为value时，如果属性不存在则执行该方法。可自定义实现，默认实现方式为抛出NSUnknownKeyException异常
> - (void)setValue:(nullable id)value forUndefinedKey:(NSString *)key;
> //设置属性名为key的属性值为nil时，如果属性为基本类型如NSInteger、int等会执行该方法。可自定义实现，默认实现方式为抛出NSInvalidArgumentException异常
> - (void)setNilValueForKey:(NSString *)key;
> @end
> ```

#### 



