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

[VFL语言](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)

> **VFL语言简介**
>
> > VFL全称是Visual Format Language，翻译过来是“可视化格式语言”，VFL是苹果公司为了简化Autolayout的编码而推出的抽象语言  
> > ![](/assets/4F9D2A32-0B3A-4B4D-BCAD-EF1D298741AB.png)
>
> **VFL的语法**
>
> > 标准间隔：\[button\]-\[textField\]
> >
> > 宽约束：\[button\(&gt;=50\)\]
> >
> > 与父视图的关系：\|-50-\[purpleBox\]-50-\|
> >
> > 垂直布局：V:\[topField\]-10-\[bottomField\]
> >
> > Flush Views：\[maroonView\]\[buleView\]
> >
> > 优先级：\[button\(100@20\)\]
> >
> > 等宽：\[button\(==button2\)\]
> >
> > Multiple Predicates：\[flexibleButton\(&gt;=70,&lt;=100\)\]
>
> VFL示例
>
> > ```
> >
> > ```

参考文献：

[https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/index.html\#//apple\_ref/doc/uid/TP40010853-CH7-SW1](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/index.html#//apple_ref/doc/uid/TP40010853-CH7-SW1)

