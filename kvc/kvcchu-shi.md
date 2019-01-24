#### 什么是KVC

> KVC\(key value coding\)键值编码是一种可以使用字符串形式来间接操作对象相关属性的方法。OC在实现KVC时没有采用实现接口的方式，而是采用非正式协议即创建一个NSObject\(NSKeyValueCoding\) 类别，通过这样的方式使得NSObject的子类可以自行实现NSKeyValueCoding类别定义的相关方法。
>
> KVC使用非常简单，但KVC却异常强大，最暗黑的功能就是它**可以无视访问限制**，无论是否为private都可以进行赋值或取值操作，readonly的属性也可以无视，提供了一种比runtime更便捷的方式来修改或访问系统级隐藏的属性，因此，经常在**开发中通过runtime获取相关属性名后使用KVC来修改那些只读readonly或隐藏的属性**。

#### KVC方法

> **主要方法**
>
> > ```
> > @interface NSObject(NSKeyValueCoding)
> >
> > @property (class, readonly) BOOL accessInstanceVariablesDirectly; //类属性默认返回YES,用来返回是否能够访问成员变量
> >
> > - (nullable id)valueForKey:(NSString *)key; //获取属性名为key的属性的值
> > - (void)setValue:(nullable id)value forKey:(NSString *)key; //设置属性名为key的属性的值为value
> >
> > - (nullable id)valueForKeyPath:(NSString *)keyPath; //获取嵌套属性属性的值
> > - (void)setValue:(nullable id)value forKeyPath:(NSString *)keyPath; //设置嵌套属性的属性的值为value
> >
> > //模型转字典: 输入一组key,返回该组key对应的Value，再转成字典返回。
> > - (NSDictionary<NSString *, id> *)dictionaryWithValuesForKeys:(NSArray<NSString *> *)keys;
> > //字典转模型: 输入一个字典，将字典中对应的value赋值给模型相应的成员变量。
> > - (void)setValuesForKeysWithDictionary:(NSDictionary<NSString *, id> *)keyedValues;
> >
> >
> > //获取属性名为key的属性值时，如果属性不存在则执行该方法。可自定义实现，默认实现方式为抛出NSUnknownKeyException异常
> > - (nullable id)valueForUndefinedKey:(NSString *)key;
> > //设置属性名为key的属性值为value时，如果属性不存在则执行该方法。可自定义实现，默认实现方式为抛出NSUnknownKeyException异常
> > - (void)setValue:(nullable id)value forUndefinedKey:(NSString *)key;
> > //设置属性名为key的属性值为nil时，如果属性为基本类型如NSInteger、int等会执行该方法。可自定义实现，默认实现方式为抛出NSInvalidArgumentException异常
> > - (void)setNilValueForKey:(NSString *)key;
> >
> > @end
> > ```
>
> **其它方法**
>
> > ```
> > //表示如果没有找到set<Key>/_set<Key>方法的话，是否会按照_key，_iskey，key，iskey的顺序搜索成员，默认返回YES。可自定义实现其get返回YES/NO
> > + (BOOL)accessInstanceVariablesDirectly;
> >
> > //KVC提供属性值正确性验证的API，它可以用来检查set的值是否正确、为不正确的值做一个替换值或者拒绝设置新值并返回错误原因。
> > //此方法的默认实现是调用validate<Key>:error:方法，如果找到此方法则调用（需要用户实现），未找到则直接返回YES
> > - (BOOL)validateValue:(inout id _Nullable * _Nonnull)ioValue forKey:(NSString *)inKey error:(out NSError **)outError;
> > - (BOOL)validateValue:(inout id _Nullable * _Nonnull)ioValue forKeyPath:(NSString *)inKeyPath error:(out NSError **)outError;
> >
> > //其他集合类的方法
> > ```

#### 



