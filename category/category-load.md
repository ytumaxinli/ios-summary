#### **+load方法加载**

> #### +load方法会在runtime加载类、分类时调用。每个类、分类的+load，在程序运行过程中只调用一次。
>
> > 先调用类的+load
> > > 按照编译先后顺序调用（先编译，先调用）
> > > 调用子类的+load之前会先调用父类的+load
> >
> > 再调用分类的+load
> > > 按照编译先后顺序调用（先编译，先调用）

####  objc4源码解读过程

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







