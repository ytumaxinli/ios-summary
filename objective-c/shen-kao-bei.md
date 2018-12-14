#### 浅拷贝：仅拷贝指向对象的指针，不拷贝对象本身

> 当原对象是不可变对象时，使用copy是仅拷贝对象的指针
>
> ```
> NSString *str1 = @"str1";
> NSString *str2 = [str1 copy];
> NSLog(@"\nstr1 = %@ str1P = %p \n str2 = %@ str2P = %p", str1, str1, str2, str2);
>
> /*输出结果，可以得出下图结论
>     str1 = str1 str1P = 0x104d75180
>     str2 = str1 str2P = 0x104d75180
> */
> ```

#### 深拷贝：拷贝对象到另一块内存中

> **遵循NSCopying、NSMutableCopying协议的系统类**
>
> > 遵循NSCopying、NSMutableCopying协议的系统类可直接调用copy或者mutableCopy方法
>
> **自定义类需要实现NSCopying、NSMutableCopying协议才能调用copy或者mutableCopy方法**
>
> > ```
> > @interface Person : NSObject<NSCopying,NSMutableCopying,NSCoding>
> >
> > @property(nonatomic, copy)NSMutableString *name;
> > @property(nonatomic, assign)NSInteger age;
> > @property(nonatomic, copy)NSString *cardId;
> >
> > @end
> >
> > @implementation Person
> > - (id)copyWithZone:(NSZone *)zone
> > {
> >     Person *person = [Person allocWithZone:zone];
> >     //由于属性中响应字段已经设置为copy属性，因此不需要copy
> >     person.name = _name;
> >     person.age = _age;
> >     person.cardId = _cardId;
> >  
> >     return person;
> > }
> >
> > - (id)mutableCopyWithZone:(NSZone *)zone
> > {
> >     Person *person = [Person allocWithZone:zone];
> >     person.name = _name;
> >     person.age = _age;
> >     person.cardId = _cardId;
> >     
> >     return person;
> > }
> > @end
> > ```

#### 面试例题

> ```
> NSArray *array = [NSArray arrayWithObjects:[NSMutableString stringWithString:@"a"],@"b",@"c", nil];
> //浅拷贝：仅拷贝数组指针
> NSArray *array1 = [array copy];
> //单层深拷贝：拷贝数组对象，不拷贝数组中的对象
> NSMutableArray *mArray1 = [array mutableCopy];
> [array1[0] appendString:@"1"];
> NSLog(@"%@,%@,%@",array[0],array1[0],mArray1[0]);
>
> //打印结果
> a1,a1,a1
>
> NSMutableArray *mArray = [NSMutableArray arrayWithObjects:[NSMutableString stringWithString:@"a"],@"b",@"c", nil];
> //浅拷贝：仅拷贝数组指针
> NSArray *array1 = [mArray copy];
> //单层深拷贝：拷贝数组对象，不拷贝数组中的对象
> NSMutableArray *mArray1 = [mArray mutableCopy];
> //重新初始化
> mArray[1] = @"b1";
> NSLog(@"%@,%@,%@",mArray[1],array1[1],mArray1[1]);
>
> //打印结果
> b1,b,b
> ```



