#### Category 底层结构

> #### ![](/assets/Category01.png)

#### Category 加载处理过程

> ![](/assets/Category02.png)**相关源码**
>
> > attachCategories
> >
> > ```
> > static void attachCategories(Class cls, category_list *cats, bool flush_caches)
> > {
> >     if (!cats) return;
> >     if (PrintReplacedMethods) printReplacements(cls, cats);
> >
> >     bool isMeta = cls->isMetaClass();
> >
> >     // fixme rearrange to remove these intermediate allocations
> >     method_list_t **mlists = (method_list_t **)
> >         malloc(cats->count * sizeof(*mlists));
> >     property_list_t **proplists = (property_list_t **)
> >         malloc(cats->count * sizeof(*proplists));
> >     protocol_list_t **protolists = (protocol_list_t **)
> >         malloc(cats->count * sizeof(*protolists));
> >
> >     // Count backwards through cats to get newest categories first
> >     int mcount = 0;
> >     int propcount = 0;
> >     int protocount = 0;
> >     int i = cats->count;
> >     bool fromBundle = NO;
> >     while (i--) {
> >         auto& entry = cats->list[i];
> >
> >         method_list_t *mlist = entry.cat->methodsForMeta(isMeta);
> >         if (mlist) {
> >             mlists[mcount++] = mlist;
> >             fromBundle |= entry.hi->isBundle();
> >         }
> >
> >         property_list_t *proplist = 
> >             entry.cat->propertiesForMeta(isMeta, entry.hi);
> >         if (proplist) {
> >             proplists[propcount++] = proplist;
> >         }
> >
> >         protocol_list_t *protolist = entry.cat->protocols;
> >         if (protolist) {
> >             protolists[protocount++] = protolist;
> >         }
> >     }
> >
> >     auto rw = cls->data();
> >
> >     prepareMethodLists(cls, mlists, mcount, NO, fromBundle);
> >     rw->methods.attachLists(mlists, mcount);
> >     free(mlists);
> >     if (flush_caches  &&  mcount > 0) flushCaches(cls);
> >
> >     rw->properties.attachLists(proplists, propcount);
> >     free(proplists);
> >
> >     rw->protocols.attachLists(protolists, protocount);
> >     free(protolists);
> > }
> > ```
> >
> > attachLists
> >
> > ```
> >  void attachLists(List* const * addedLists, uint32_t addedCount) {
> >         if (addedCount == 0) return;
> >
> >         if (hasArray()) {
> >             // many lists -> many lists
> >             uint32_t oldCount = array()->count;
> >             uint32_t newCount = oldCount + addedCount;
> >             setArray((array_t *)realloc(array(), array_t::byteSize(newCount)));
> >             array()->count = newCount;
> >             memmove(array()->lists + addedCount, array()->lists, 
> >                     oldCount * sizeof(array()->lists[0]));
> >             memcpy(array()->lists, addedLists, 
> >                    addedCount * sizeof(array()->lists[0]));
> >         }
> >         else if (!list  &&  addedCount == 1) {
> >             // 0 lists -> 1 list
> >             list = addedLists[0];
> >         } 
> >         else {
> >             // 1 list -> many lists
> >             List* oldList = list;
> >             uint32_t oldCount = oldList ? 1 : 0;
> >             uint32_t newCount = oldCount + addedCount;
> >             setArray((array_t *)malloc(array_t::byteSize(newCount)));
> >             array()->count = newCount;
> >             if (oldList) array()->lists[addedCount] = oldList;
> >             memcpy(array()->lists, addedLists, 
> >                    addedCount * sizeof(array()->lists[0]));
> >         }
> >     }
> > ```



