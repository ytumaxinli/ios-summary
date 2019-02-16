#### **+load方法加载**

> #### +load方法会在runtime加载类、分类时调用。每个类、分类的+load，在程序运行过程中只调用一次。
>
> > 先调用类的+load
> >
> > > 按照编译先后顺序调用（先编译，先调用）  
> > > 调用子类的+load之前会先调用父类的+load
> >
> > 再调用分类的+load
> >
> > > 按照编译先后顺序调用（先编译，先调用）

#### 

#### 源码调用过程

> objc-os.mm -&gt; void \_objc\_init\(void\)                                                                           //runtime源码入口
>
> void load\_images\(constchar \*path \_\_unused, conststructmach\_header \*mh\)     //加载动态库
>
> > void prepare\_load\_methods\(constheaderType \*mhdr\)                                       //获取需要加载load方法的类和分类
> >
> > > static void schedule\_class\_load\(Class cls\)                                                    //递归遍历类的继承关系
> > >
> > > void add\_class\_to\_loadable\_list\(Class cls\)                                                    //存储有+load方法类
> > >
> > > void add\_category\_to\_loadable\_list\(Category cat\)                                        //存储有+load方法的分类
> >
> > void call\_load\_methods\(void\)                                                                                //调用load方法
> >
> > > static void call\_class\_loads\(void\)                                                                    //调用类load方法
> > >
> > > static bool call\_category\_loads\(void\)                                                             //调用分类load方法



#### prepare\_load\_methods 源码解读

> ```
> prepare_load_methods
> {
>     size_t count, i;
>
>     runtimeLock.assertLocked();
>     //获取不能懒加载的类列表
>     classref_t *classlist = _getObjc2NonlazyClassList(mhdr, &count);
>     for (i = 0; i < count; i++) {
>         //遍历类列表，将有+load方法的类存入相关数组
>         schedule_class_load(remapClass(classlist[i]));
>     }
>
>     //获取不能懒加载的分类列表
>     category_t **categorylist = _getObjc2NonlazyCategoryList(mhdr, &count);
>     //遍历分类列表,将有+load方法的分类存入相关数组
>     for (i = 0; i < count; i++) {
>         category_t *cat = categorylist[i];
>         Class cls = remapClass(cat->cls);
>         if (!cls) continue;  // category for ignored weak-linked class
>         realizeClass(cls);
>         assert(cls->ISA()->isRealized());
>         add_category_to_loadable_list(cat);
>     }
> }
> ```
>
> > schedule\_class\_load
> >
> > ```
> > static void schedule_class_load(Class cls)
> > {
> >     if (!cls) return;
> >     assert(cls->isRealized());  // _read_images should realize
> >     //获取该类flag标示，判断+load方法是否被加载过（该类是否已经被存储到数组中）
> >     if (cls->data()->flags & RW_LOADED) return;
> >
> >     //递归调用父类，确保父类load方法存储到数组前面首先被调用
> >     schedule_class_load(cls->superclass);
> >     //将改类存入数组中，供call_class_loads方法进行遍历调用
> >     add_class_to_loadable_list(cls);
> >     
> >     //设置flag标示该类已被存储到数组中
> >     cls->setInfo(RW_LOADED); 
> > }
> > ```
> >
> > > add\_class\_to\_loadable\_list
> > >
> > > ```
> > > void add_class_to_loadable_list(Class cls)
> > > {
> > >     IMP method;
> > >
> > >     loadMethodLock.assertLocked();
> > >     //判断该类是否有+load方法，没有直接返回
> > >     method = cls->getLoadMethod();
> > >     if (!method) return;  // Don't bother if cls has no +load method
> > >     
> > >     if (PrintLoading) {
> > >         _objc_inform("LOAD: class '%s' scheduled for +load", 
> > >                      cls->nameForLogging());
> > >     }
> > >     
> > >     if (loadable_classes_used == loadable_classes_allocated) {
> > >         loadable_classes_allocated = loadable_classes_allocated*2 + 16;
> > >         loadable_classes = (struct loadable_class *)
> > >             realloc(loadable_classes,
> > >                               loadable_classes_allocated *
> > >                               sizeof(struct loadable_class));
> > >     }
> > >     //如果有+load方法将类与load方法IMP 赋值给loadable_class结构体，并将结构体存入数组
> > >     loadable_classes[loadable_classes_used].cls = cls;
> > >     loadable_classes[loadable_classes_used].method = method;
> > >     loadable_classes_used++;
> > > }
> > > ```
> >
> > add\_category\_to\_loadable\_list
> >
> > ```
> > void add_category_to_loadable_list(Category cat)
> > {
> >     IMP method;
> >
> >     loadMethodLock.assertLocked();
> >     //在分类中获取+load方法，如果没找到直接返回
> >     method = _category_getLoadMethod(cat);
> >
> >     // Don't bother if cat has no +load method
> >     if (!method) return;
> >
> >     if (PrintLoading) {
> >         _objc_inform("LOAD: category '%s(%s)' scheduled for +load", 
> >                      _category_getClassName(cat), _category_getName(cat));
> >     }
> >     
> >     if (loadable_categories_used == loadable_categories_allocated) {
> >         loadable_categories_allocated = loadable_categories_allocated*2 + 16;
> >         loadable_categories = (struct loadable_category *)
> >             realloc(loadable_categories,
> >                               loadable_categories_allocated *
> >                               sizeof(struct loadable_category));
> >     }
> >    //如果有+load方法将分类与load方法赋值给loadable_category结构体，并将结构体存入数组
> >     loadable_categories[loadable_categories_used].cat = cat;
> >     loadable_categories[loadable_categories_used].method = method;
> >     loadable_categories_used++;
> > }
> > ```



#### call\_load\_methods

> ```
> void call_load_methods(void)
> {
>     static bool loading = NO;
>     bool more_categories;
>
>     loadMethodLock.assertLocked();
>
>     // Re-entrant calls do nothing; the outermost call will finish the job.
>     if (loading) return;
>     loading = YES;
>
>     void *pool = objc_autoreleasePoolPush();
>
>     do {
>         // 1. Repeatedly call class +loads until there aren't any more
>         // 先调用类的load方法，由于父类存储在子类前面，因此先调用父类
>         while (loadable_classes_used > 0) {
>             //调用类的load方法
>             call_class_loads();
>         }
>
>         // 2. Call category +loads ONCE
>         // 再调用分类的load方法
>         more_categories = call_category_loads();
>
>         // 3. Run more +loads if there are classes OR more untried categories
>     } while (loadable_classes_used > 0  ||  more_categories);
>
>     objc_autoreleasePoolPop(pool);
>
>     loading = NO;
> }
> ```
>
>
>
> > call\_class\_loads
> >
> > ```
> > static void call_class_loads(void)
> > {
> >     int i;
> >     
> >     // Detach current loadable list.
> >     struct loadable_class *classes = loadable_classes;
> >     int used = loadable_classes_used;
> >     loadable_classes = nil;
> >     loadable_classes_allocated = 0;
> >     loadable_classes_used = 0;
> >     
> >     //遍历loadable_classes数组，调用数组中所有类的load方法
> >     // Call all +loads for the detached list.
> >     for (i = 0; i < used; i++) {
> >         //获取到Class
> >         Class cls = classes[i].cls;
> >         //获取IMP
> >         load_method_t load_method = (load_method_t)classes[i].method;
> >         if (!cls) continue; 
> >
> >         if (PrintLoading) {
> >             _objc_inform("LOAD: +[%s load]\n", cls->nameForLogging());
> >         }
> >         //直接调用方法指针调用load IMP
> >         (*load_method)(cls, SEL_load);
> >     }
> >
> >     //销毁数组    
> >     // Destroy the detached list.
> >     if (classes) free(classes);
> > }
> > ```
> >
> > call\_category\_loads
> >
> > ```
> > static bool call_category_loads(void)
> > {
> >     int i, shift;
> >     bool new_categories_added = NO;
> >     
> >     // Detach current loadable list.
> >     struct loadable_category *cats = loadable_categories;
> >     int used = loadable_categories_used;
> >     int allocated = loadable_categories_allocated;
> >     loadable_categories = nil;
> >     loadable_categories_allocated = 0;
> >     loadable_categories_used = 0;
> >
> >     // Call all +loads for the detached list.
> >     for (i = 0; i < used; i++) {
> >         //获取分类
> >         Category cat = cats[i].cat;
> >         //获取load方法IMP
> >         load_method_t load_method = (load_method_t)cats[i].method;
> >         Class cls;
> >         if (!cat) continue;
> >
> >         //通过分类获取类
> >         cls = _category_getClass(cat);
> >         if (cls  &&  cls->isLoadable()) {
> >             if (PrintLoading) {
> >                 _objc_inform("LOAD: +[%s(%s) load]\n", 
> >                              cls->nameForLogging(), 
> >                              _category_getName(cat));
> >             }
> >             //直接调用load方法IMP
> >             (*load_method)(cls, SEL_load);
> >             cats[i].cat = nil;
> >         }
> >     }
> >
> >     // Compact detached list (order-preserving)
> >     shift = 0;
> >     for (i = 0; i < used; i++) {
> >         if (cats[i].cat) {
> >             cats[i-shift] = cats[i];
> >         } else {
> >             shift++;
> >         }
> >     }
> >     used -= shift;
> >
> >     // Copy any new +load candidates from the new list to the detached list.
> >     new_categories_added = (loadable_categories_used > 0);
> >     for (i = 0; i < loadable_categories_used; i++) {
> >         if (used == allocated) {
> >             allocated = allocated*2 + 16;
> >             cats = (struct loadable_category *)
> >                 realloc(cats, allocated *
> >                                   sizeof(struct loadable_category));
> >         }
> >         cats[used++] = loadable_categories[i];
> >     }
> >
> >     // Destroy the new list.
> >     // 销毁数组
> >     if (loadable_categories) free(loadable_categories);
> >
> >     // Reattach the (now augmented) detached list. 
> >     // But if there's nothing left to load, destroy the list.
> >     if (used) {
> >         loadable_categories = cats;
> >         loadable_categories_used = used;
> >         loadable_categories_allocated = allocated;
> >     } else {
> >         if (cats) free(cats);
> >         loadable_categories = nil;
> >         loadable_categories_used = 0;
> >         loadable_categories_allocated = 0;
> >     }
> >
> >     if (PrintLoading) {
> >         if (loadable_categories_used != 0) {
> >             _objc_inform("LOAD: %d categories still waiting for +load\n",
> >                          loadable_categories_used);
> >         }
> >     }
> >
> >     return new_categories_added;
> > }
> > ```





