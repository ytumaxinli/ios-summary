* #### 简介

> Autolayout的功能比Autoresizing强大很多，Autolayout解决任何控件之间的相对关系问题。Autolayout的2个核心概念
>
> * 约束: 通过给控件添加约束,来决定控件的位置和尺寸
> * 参照: 在添加约束时,是依照谁来添加\(可以是父控件或者兄弟控件\)

* #### AutoLayout

> **自动布局的核心计算公式**
>
> > obj1.property1 =（obj2.property2 \* multiplier）+ constant value
>
> **添加约束的规则  **
>
> > * 对于两个同层级view之间的约束关系，添加到它们的父view上
> >
> > ![](/assets/image8.jpeg)
> >
> > * 对于两个不同层级view之间的约束关系，添加到他们最近的共同父view上
> >
> > ![](/assets/image9.jpeg)
> >
> > * 对于有层次关系的两个view之间的约束关系，添加到层次较高的父view上
> >
> > ![](/assets/image10.jpeg)

* #### NSLayoutConstraint

> 一个NSLayoutConstraint对象就代表一个约束,创建约束对象的常用方法
>
> ```
> +(id)constraintWithItem:(id)view1                             //要约束的控件
>               attribute:(NSLayoutAttribute)attr1              //约束的类型（做怎样的约束）
>               relatedBy:(NSLayoutRelation)relation            //与参照控件之间的关系  
>               toItem:(id)view2                                //参照的控件
>               attribute:(NSLayoutAttribute)attr2              //约束的类型（做怎样的约束）
>               multiplier:(CGFloat)multiplier                  //乘数
>               constant:(CGFloat)c;                            //常量
>
> //示例:view1顶部相对于view2的顶部向下偏移10
> //1 view1是view2的父view,因此contraint添加到view1上
> [self.view1 addConstraint:[NSLayoutConstraint constraintWithItem:self.view2
>                                                        attribute:NSLayoutAttributeTop
>                                                        relatedBy:NSLayoutRelationEqual
>                                                           toItem:self.view1
>                                                        attribute:NSLayoutAttributeTop
>                                                       multiplier:1
>                                                         constant:10]];
>                                                         
> NSLayoutConstraint *constratin4 = [NSLayoutConstraint constraintWithItem:self.view1 attribute:NSLayoutAttributeHeight relatedBy:NSLayoutRelationEqual toItem:nil attribute:NSLayoutAttributeNotAnAttribute multiplier:1 constant:50];
> ```



