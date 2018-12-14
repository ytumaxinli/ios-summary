#### **系统类copy**

> **遵循NSCopying、NSMutableCopying协议的系统类可直接调用copy或者mutableCopy方法**
>
> [https://developer.apple.com/documentation/foundation/nscopying?language=objc](https://developer.apple.com/documentation/foundation/nscopying?language=objc)

#### **自定义类copy**

> **自定义类需要实现NSCopying、NSMutableCopying协议才能调用copy或者mutableCopy方法**
>
> ```
> @property(nonatomic, copy)NSMutableString *name;
> @property(nonatomic, assign)NSInteger age;
> @property(nonatomic, copy)NSString *cardId;
>
> @end
>
> @implementation Person
> - (id)copyWithZone:(NSZone *)zone
> {
>     Person *person = [Person allocWithZone:zone];
>     //由于属性中响应字段已经设置为copy属性，因此不需要copy
>     person.name = _name;
>     person.age = _age;
>     person.cardId = _cardId;
>  
>     return person;
> }
>
> - (id)mutableCopyWithZone:(NSZone *)zone
> {
>     Person *person = [Person allocWithZone:zone];
>     person.name = _name;
>     person.age = _age;
>     person.cardId = _cardId;
>     
>     return person;
> }
> @end
> ```

#### 浅拷贝：仅拷贝指向对象的指针，不拷贝对象本身

> **当原对象是不可变对象时，使用copy是仅拷贝对象的指针**
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

> **深拷贝的情况**
>
> > **可变对象使用copy方法，生成不可变对象时为深拷贝**
> >
> > > ```
> > > NSArray *array = [mArray copy];
> > > NSLog(@"%p, %p",mArray, array);
> > >
> > > //打印结果
> > > 0x600003da6af0, 0x600003da6ac0
> > > ```
> >
> > **使用mutableCopy方法拷贝时为深拷贝**
> >
> > > ```
> > > NSArray *array = [NSArray arrayWithObjects:@"a",@"b",@"c", nil];
> > > NSArray *cArray = [array mutableCopy];
> > > NSLog(@"%p, %p",array, cArray);
> > >
> > > NSMutableArray *mArray = [NSMutableArray arrayWithObjects:@"a",@"b",@"c", nil];
> > > NSArray *cMarray = [mArray mutableCopy];
> > > NSLog(@"%p, %p",mArray, cMarray);
> > >
> > > //打印结果
> > > 0x600001f6b6f0, 0x600001f6b6c0
> > > 0x600001f6b690, 0x600001f6b5a0
> > > ```
>
> **单层深拷贝**
>
> > 普通的集合类进行调用copy或mutableCopy时，只是拷贝了集合自己的对象。并没有拷贝集合内的对象，因此叫单层深拷贝
> >
> > ```
> > NSArray *array = [NSArray arrayWithObjects:@"a",@"b",@"c", nil];
> > NSArray *cArray = [array mutableCopy];
> > NSLog(@"array:%p, cArray:%p, array.firstObject:%p, cArray.firstObject:%p",array, cArray, array.firstObject, cArray.firstObject);
> >
> > NSMutableArray *mArray = [NSMutableArray arrayWithObjects:@"1",@"2",@"3", nil];
> > NSArray *cMarray = [mArray mutableCopy];
> > NSLog(@"mArray:%p, cMarray:%p, mArray.firstObject:%p, cMarray.firstObject:%p",mArray, cMarray, mArray.firstObject, cMarray.firstObject);
> >
> > //打印结果
> > array:0x60000192b090, cArray:0x60000192b060, array.firstObject:0x10c584280, cArray.firstObject:0x10c584280
> > mArray:0x60000197f870, cMarray:0x60000197f9c0, mArray.firstObject:0x10c584300, cMarray.firstObject:0x10c584300
> > ```
>
> **完全深拷贝**
>
> > 方法一：- \(instancetype\)initWithArray:\(NSArray&lt;ObjectType&gt; \*\)array copyItems:\(BOOL\)flag;
> >
> > ```
> > If YES, each object in array receives acopyWithZone:message to create a copy of the object—objects must conform to the NSCopying protocol. In a managed memory environment, this is instead of the retain message the object would otherwise receive. The object copy is then added to the returned array.
> > If NO, then in a managed memory environment each object inarraysimply receives aretainmessage when it is added to the returned array.
> > ```
> >
> > ```
> > NSMutableArray *array1 = [NSMutableArray arrayWithObjects:[NSMutableString stringWithString:@"a"],@"b",@"c", nil];
> > NSMutableArray *array2 = [[NSMutableArray alloc] initWithArray:array1 copyItems:YES];
> > NSLog(@"%p %p",array1,array2);
> > NSLog(@"%p %p",array1[0],array2[0]);
> >
> > //打印结果
> > 0x6000023de8e0 0x6000023de8b0
> > 0x6000023de910 0x9a838cca1910ed4a
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



