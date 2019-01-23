```
//获取属性名为key的属性值时，如果属性不存在则执行该方法。可自定义实现，默认实现方式为抛出NSUnknownKeyException异常
- (nullable id)valueForUndefinedKey:(NSString *)key;
//设置属性名为key的属性值为value时，如果属性不存在则执行该方法。可自定义实现，默认实现方式为抛出NSUnknownKeyException异常
- (void)setValue:(nullable id)value forUndefinedKey:(NSString *)key;
//设置属性名为key的属性值为nil时，如果属性为基本类型如NSInteger、int等会执行该方法。可自定义实现，默认实现方式为抛出NSInvalidArgumentException异常
- (void)setNilValueForKey:(NSString *)key;
```



