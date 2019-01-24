#### **KVC取值原理**

> **流程描述：**方法的默认实现如下
>
> > 1 在消息接收者&lt;方法调用者&gt;的类对象中依次寻找符合`-get<Key>, -<key>, or -is<Key>`格式的方法。
> >
> > > 如果以上的任一方法被找到则会进行调用，调用结束后会对方法的返回结果进行处理
> > >
> > > 1.1 如果结果类型是对象类型，则将结果直接返回。
> > >
> > > 1.2 如果结果类型是NSNumber支持的标量类型\(NSInteger/char/int/BOOL等\)，则会将该结果转换成NSNumber类型的值进行返回。
> > >
> > > 1.3 如果结果类型是NSNumber不支持的标量类型\(CGRect/CGSize/CGPoint/NRange等\)，则会将该结果转化为NSValue类型的值进行返回。
> >
> > 2 如果之上的方法未找到，则在类对象中寻找符合`-countOf<Key> 、 -indexIn<Key>OfObject: 、 -objectIn<Key>AtIndex:`  
> > `-<key>AtIndexes: (corresponding to NSOrderedSet)`这些格式的方法。如果找到count方法和indexOf方法以及其他两个可能的方法中的至少一个，则返回响应所有NSOrderedSet方法的集合代理对象。每个发送到集合代理对象的NSOrderedSet消息将导致以上四个方法的某种组合消息被发送到`-valueForKey：`消息的原始接收者上。如果接收者的类还实现了一个名称匹配-get &lt;Key&gt;：range：格式的可选方法。则该方法将在获得最佳性能时使用。
> >
> > 3 如果以上方法未找到，则在类对象中寻找符合`-countOf<Key> and -objectIn<Key>AtIndex: -<key>AtIndexes:`这些格式的方法。如果找到`count`方法并且找到其他两个可能方法中的至少一个，则返回响应所有NSArray方法的集合代理对象。发送到每个集合代理对象的NSArray消息将导致以上三个方法的某种组合消息被发送到-valueForKey：消息的原始接收者上。如果接收者的类还实现了一个名称匹配`-get <Key>：range：`格式的可选方法。则该方法将在获得最佳性能时使用。
> >
> > 4  如果以上方法未找到，则在类对象中`-countOf <Key>，-enumeratorOf <Key>和-memberOf <Key> :`\(对应于NSSet类定义的基本方法）。 如果找到所有三个这样的方法，则返回响应所有NSSet方法的集合代理对象。 每个发送到集合代理对象的NSSet消息将导致以上三个方法的某种组合消息被发送到-valueForKey：的原始接收者上。
> >
> > 5 如果以上方法未找到，并且消息接受者的`accessInstanceVariablesDirectly`**类属性**返回YES时（此类属性默认值为YES,也可以通过重写类属性的get方法修改返回值），则在类对象中按顺序寻找符合`_<key>, _is<Key>, <key>, or is<Key>`的成员变量。如果找到对应的成员变量，则返回接收器中实例变量的值，**与步骤1中的NSNumber或NSValue的转换类型相同。**
> >
> > 6 如果以上均未找到，调用`-valueForUndefinedKey:`返回结果。valueForUndefinedKey的默认实现是抛出未找到对应Key的异常，但是我们可以在自己的应用中重写这个方法
> >
> > **兼容性说明:**
> >
> > 为了向前兼容在步骤1与步骤3之间会搜索符合-\_get&lt;Key&gt;, or -\_&lt;key&gt;格式的访问器。如果发现了符合的方法并且调用，需要将返回结果按照步骤1一样进行转化。**从Mac OS 10.3开始，不推荐使用名称以下划线开头的KVC访问器方法**。
> >
> > **总结：**
> >
> > > 1 使用valueForKey方法时会首先在调用者的类对象中依次寻找符合 -get&lt;key&gt;、-&lt;key&gt;、-is&lt;key&gt;格式的方法，根据向下兼容性原则还会搜索目前已经不推荐的格式的方法-\_get&lt;Key&gt;、-\_&lt;key&gt;。
> > >
> > > 2 valueForKey方法的返回结果是id类型，因此如果对应的方法返回的不是对象类型会被转化为NSNumber或者NSValue类型。
> > >
> > > 3 2-4的描述针对的是集合类型的kvc。但是非集合类型调用valueForKey依然会访问这些方法。
> > >
> > > 4 5中`@property (class, readonly) BOOL accessInstanceVariablesDirectly;`为类属性。其默认返回值是YES,可以通过重写类方法`+(BOOL)accessInstanceVariablesDirectly{returnNO;}`进行修改。当此值返回YES是会依次寻找符合\_&lt;key&gt;、\_is&lt;key&gt;、&lt;key&gt;、is&lt;key&gt;格式的变量直到寻找到任一一个。返回结果同1需要进行转化。
> > >
> > > 4 如果都为寻找到会调用`- valueForUndefinedKey:`方法，默认会抛出异常，该方法可以重写。

#### **KVC设值的原理**

> **流程描述**
>
> > 1在消息接收者&lt;方法调用者&gt;的类对象中寻找符合-set&lt;Key&gt;:.格式的方法。
> >
> > > 如果找到了符合以上格式的方法，则检查方法参数的格式。
> > >
> > > 1.1 如果格式不是对象类型：
> > >
> > > > 1.1.1 当设置的value值为nil，则会调用-setNilValueForKey:方法。该方法的默认实现是抛出无效参数的异常（NSInvalidArgumentException）,但是我们可以重写此方法来防止闪退。
> > > >
> > > > 1.1.2 当value非nil时，在set方法调用前，需要做与-valueForKey:中相反的操作，即将NSNumber/NSValue进行反向转化为标准类型。
> > >
> > > 1.2 如果格式是对象类型，则直接调用此方法将value传入就可以了。
> >
> > 2 如果之上的方法未找到，且类属性+accessInstanceVariablesDirectly返回YES，则会在类对象中依次寻找符合\_&lt;key&gt;, \_is&lt;Key&gt;, &lt;key&gt;, or is&lt;Key&gt;格式的成员变量。
> >
> > > 如果任一一个符合的成员变量被找到，则会对成员变量的类型进行判断
> > >
> > > 2.1 如果类型为对象类型，则value值被retain并且在成员变量的旧值被release之后将retain的value设置给成员变量。
> > >
> > > 2.2 如果成员变量的类型为其他类型则会像第1步一样将NSNumber or NSValue进行反向转化后赋值给成员变量。
> >
> > 3 如果以上都为找到， 系统会调用-setValue:forUndefinedKey:方法。该方法默认实现是抛出未定义key异常，我们可以通过重写该方法来避免crash.
> >
> > **兼容性说明：**
> >
> > 在步骤1中也寻找符合-\_set &lt;Key&gt;：格式的方法。自Mac OS 10.3起，不推荐使用名称以下划线开头的KVC访问器方法。
>
> ![](/assets/KVC01.png)
>
> ![](/assets/KVC01.png)

#### 问题

> ![](/assets/KVC03.png)



