**分类添加成员变量**

> 默认情况下，因为分类底层结构的限制，不能添加成员变量到分类中。但可以通过关联对象来间接实现
>
> 关联对象提供了以下API
>
> > **添加关联对象**
> > `void objc_setAssociatedObject(id object, const void* key, id value, objc_AssociationPolicy policy)`
> > **获得关联对象**
> > `id objc_getAssociatedObject(id object, const void* key)`
> > **移除所有的关联对象**
> > `void objc_removeAssociatedObjects(id object)`



**Key的常见用法**

> ```
> static void *MyKey = &MyKey;
> objc_setAssociatedObject(obj, MyKey, value, OBJC_ASSOCIATION_RETAIN_NONATOMIC)
> objc_getAssociatedObject(obj, MyKey)
>
> static char MyKey;
> objc_setAssociatedObject(obj, &MyKey, value, OBJC_ASSOCIATION_RETAIN_NONATOMIC)
> objc_getAssociatedObject(obj, &MyKey)
>
> 使用属性名作为key
> objc_setAssociatedObject(obj, @"property", value, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
> objc_getAssociatedObject(obj, @"property");
>
> 使用get方法的@selecor作为key
> objc_setAssociatedObject(obj, @selector(getter), value, OBJC_ASSOCIATION_RETAIN_NONATOMIC)
> objc_getAssociatedObject(obj, _cmd)
> ```



**objc\_AssociationPolicy - 关联策略**

> | objc\_AssociationPolicy | 对应的修饰符 |
> | :---: | :---: |
> | OBJC\_ASSOCIATION\_ASSIGN | assign |
> | OBJC\_ASSOCIATION\_RETAIN\_NONATOMIC | strong, nonatomic |
> | OBJC\_ASSOCIATION\_COPY\_NONATOMIC | copy, nonatomic |
> | OBJC\_ASSOCIATION\_RETAIN | strong, atomic |
> | OBJC\_ASSOCIATION\_COPY | copy, atomic |



**关联对象的原理**

> 实现关联对象技术的核心对象
>
> > AssociationsManager
> >
> > AssociationsHashMap
> >
> > ObjectAssociationMap
> >
> > ObjcAssociation
>
> objc4 源码解读：objc-references.mm
>
> > ```
> > class AssociationsManager {
> >     // associative references: object pointer -> PtrPtrHashMap.
> >     static AssociationsHashMap *_map;
> > public:
> >     AssociationsManager()   { AssociationsManagerLock.lock(); }
> >     ~AssociationsManager()  { AssociationsManagerLock.unlock(); }
> >     
> >     AssociationsHashMap &associations() {
> >         if (_map == NULL)
> >             _map = new AssociationsHashMap();
> >         return *_map;
> >     }
> > };
> >
> > class AssociationsHashMap : public unordered_map<disguised_ptr_t, ObjectAssociationMap *, DisguisedPointerHash, DisguisedPointerEqual, AssociationsHashMapAllocator> {
> > public:
> >     void *operator new(size_t n) { return ::malloc(n); }
> >     void operator delete(void *ptr) { ::free(ptr); }
> > };
> >
> > class ObjectAssociationMap : public std::map<void *, ObjcAssociation, ObjectPointerLess, ObjectAssociationMapAllocator> {
> > public:
> >     void *operator new(size_t n) { return ::malloc(n); }
> >     void operator delete(void *ptr) { ::free(ptr); }
> > };
> > ```
>
> ![](/assets/Association01.png)



