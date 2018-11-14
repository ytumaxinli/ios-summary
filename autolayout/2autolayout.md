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

> 一个NSLayoutConstraint对象就代表一个约束。
>
> **使用NSLayoutConstraint注意事项：**
>
> > 需要添加约束的view,需要禁止translatesAutoresizingMaskIntoConstraints属性。
> >
> > 如果约束不需要第二个view 和 属性是，可以设置view2为nil、attr2为NSLayoutAttributeNotAnAttribute
> >
> > ```
> > If the constraint you wish to express does not have a second view and attribute, 
> > use nil and NSLayoutAttributeNotAnAttribute
> > ```
>
> **创建约束对象的常用方法**
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
> //示例:view2顶部相对于view1的顶部向下偏移10，view1是view2的父view,因此contraint添加到view1上。
> [self.view2 setTranslatesAutoresizingMaskIntoConstraints:NO];
> [self.view1 addConstraint:[NSLayoutConstraint constraintWithItem:self.view2
>                                                        attribute:NSLayoutAttributeTop
>                                                        relatedBy:NSLayoutRelationEqual
>                                                           toItem:self.view1
>                                                        attribute:NSLayoutAttributeTop
>                                                       multiplier:1
>                                                         constant:10]];
> //示例：view2的高度为50
> [NSLayoutConstraint constraintWithItem:self.view2 
>                              attribute:NSLayoutAttributeHeight 
>                              relatedBy:NSLayoutRelationEqual 
>                              toItem:nil 
>                              attribute:NSLayoutAttributeNotAnAttribute 
>                              multiplier:1 
>                              constant:50];
> ```

#### 

VFL语言

> **VFL语言简介**
>
> > VFL全称是Visual Format Language，翻译过来是“可视化格式语言”，VFL是苹果公司为了简化Autolayout的编码而推出的抽象语言  
> > ![](/assets/4F9D2A32-0B3A-4B4D-BCAD-EF1D298741AB.png)
>
> VFL示例
>
> > ```
> > H:[cancelButton(72)]-12-[acceptButton(50)]
> > canelButton宽72，acceptButton宽50，它们之间间距12
> > ```
> >
> > ```
> > H:[wideView(>=60@700)]
> > wideView宽度大于等于60point，该约束条件优先级为700（优先级最大值为1000，优先级越高的约束越先被满足）
> > ```
> >
> > ```
> > V:[redBox][yellowBox(==redBox)]
> > 竖直方向上，先有一个redBox，其下方紧接一个高度等于redBox高度的yellowBox
> > ```
> >
> > ```
> > H:|-10-[Find]-[FindNext]-[FindField(>=20)]-|
> > 水平方向上，Find距离父view左边缘默认间隔宽度，之后是FindNext距离Find间隔默认宽度；再之后是宽度不小于20的FindField，它和FindNext以及父view右边缘的间距都是默认宽度。（竖线“|”表示superview的边缘）
> > ```

参考文献：

https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/index.html\#//apple\_ref/doc/uid/TP40010853-CH7-SW1



