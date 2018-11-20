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
>                              multiplier:0 
>                              constant:50];
> ```

#### 

[VFL语言](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)

> **VFL语言简介**
>
> > VFL全称是Visual Format Language，翻译过来是“可视化格式语言”，VFL是苹果公司为了简化Autolayout的编码而推出的抽象语言![](/assets/4F9D2A32-0B3A-4B4D-BCAD-EF1D298741AB.png)
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
> **VFL的使用**
>
> > 使用VFL来创建约束数组
> >
> > ```
> > + (NSArray *)constraintsWithVisualFormat:(NSString *)format           //VFL语句                                                                                        
> >                                  options:(NSLayoutFormatOptions)opts  //约束类型 
> >                                  metrics:(NSDictionary *)metrics      //VFL语句中用到的具体数值
> >                                  views:(NSDictionary *)views;         //VFL语句中用到的控件
> >
> > //创建一个字典（内部包含VFL语句中用到的控件）的快捷宏定义
> > NSDictionaryOfVariableBindings(...)
> > ```
>
> VFL示例
>
> > ```
> > //禁止AutoresizingMask自动转化为约束
> > [self.viewRed setTranslatesAutoresizingMaskIntoConstraints:NO];
> > [self.viewBlue setTranslatesAutoresizingMaskIntoConstraints:NO];
> > //_viewRed、_viewRed 1.宽度相等 2.顶底对齐 3分别距离父View左右边距及它们之间距离均为30
> > [self.viewBase addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"H:|-gaps-[_viewRed(==_viewBlue)]-gaps-[_viewBlue]-gaps-|"
> >                                                                       options:NSLayoutFormatAlignAllTop | NSLayoutFormatAlignAllBottom
> >                                                                       metrics:@{@"gaps":@30}
> >                                                                       views:NSDictionaryOfVariableBindings(_viewRed,_viewBlue)]];
> > //_viewRed 1.高度为50 2.距离父底边10
> > [self.viewBase addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"V:[_viewRed(height)]-gaps-|"
> >                                                                       options:kNilOptions
> >                                                                       metrics:@{@"height":@50,@"gaps":@10}
> >                                                                       views:NSDictionaryOfVariableBindings(_viewRed)]];
> > ```

* #### AutoLayout优先级

> ```
> //约束最大优先级1000，不能超过此值：A required constraint.  Do not exceed this.
> static const UILayoutPriority UILayoutPriorityRequired NS_AVAILABLE_IOS(6_0) = 1000; 
>
> //button抗拒压缩的优先级：This is the priority level with which a button resists compressing its content.
> static const UILayoutPriority UILayoutPriorityDefaultHigh NS_AVAILABLE_IOS(6_0) = 750;
>
> //button抗拒拉伸的优先级：This is the priority level at which a button hugs its contents horizontally. 
> static const UILayoutPriority UILayoutPriorityDefaultLow NS_AVAILABLE_IOS(6_0) = 250; 
>
> //uiview通过调用-[UIView systemLayoutSizeFittingSize:]，给View设置尺寸所产生的约束优先级。
> // When you send -[UIView systemLayoutSizeFittingSize:], the size fitting most closely to the target size (the argument) is computed.  UILayoutPriorityFittingSizeLevel is the priority level with which the view wants to conform to the target size in that computation.  It's quite low.  It is generally not appropriate to make a constraint at exactly this priority.  You want to be higher or lower
> static const UILayoutPriority UILayoutPriorityFittingSizeLevel NS_AVAILABLE_IOS(6_0) = 50;
> ```

* #### 安全边界

> **iOS7  **topLayoutGuide、bottomLayoutGuide
>
> > **声明**
> >
> > > ```
> > > @interface UIViewController (UILayoutSupport)
> > > // These objects may be used as layout items in the NSLayoutConstraint API
> > > @property(nonatomic,readonly,strong)id<UILayoutSupport> topLayoutGuideAPI_DEPRECATED("Use view.safeAreaLayoutGuide.topAnchor instead of topLayoutGuide.bottomAnchor", ios(7.0,11.0), tvos(7.0,11.0));
> > > @property(nonatomic,readonly,strong)id<UILayoutSupport> bottomLayoutGuideAPI_DEPRECATED("Use view.safeAreaLayoutGuide.bottomAnchor instead of bottomLayoutGuide.topAnchor", ios(7.0,11.0), tvos(7.0,11.0));
> > > ....
> > > @end
> > > ```
> >
> > **topLayoutGuide**
> >
> > > * 只有当view controller在屏幕最前面时，vc的topLayoutGuide属性才会发挥作用。topLayoutGuide是一个虚拟view，用来表示不希望在半透明或透明UIKit栏后面显示的内容的最高垂直范围（例如状态或导航栏）。
> > >
> > > * 使用NSLayoutConstraint布局时可以将topLayoutGuide作为约束的一个constraint item使用。如：`[NSLayoutConstraint constraintWithItem:self.collectionView attribute:NSLayoutAttributeTop relatedBy:NSLayoutRelationLessThanOrEqual toItem:self.topLayoutGuide attribute:NSLayoutAttributeBottom multiplier:1 constant:0];`
> > >
> > > * topLayoutGuide的值可以通过调用self.topLayoutGuide.length获得。这个length由视图控制器或其封闭的容器视图控制器（navigation 或 tabbar controller）进行约束。具体如下：
> > >
> > > * > 1没有被包裹的view controller，这个属性指的是status bar\(如果status bar显示的情况下\)的底部或者是view controller's view的上边缘。  
> > >   > 2 被包裹的view controller不约束这个属性，相反包裹view controller来约束这个属性用来指示：
> > >   >
> > >   > > 2.1 navigation bar的底部，如果navigation bar可见
> > >   > >
> > >   > > 2.2 status bar的底部，如果只用status bar可见
> > >   > >
> > >   > > 2.3 view controller's view上边缘，如果status bar 、navigation bar均可见
> > >   >
> > >   > 3 如果包裹控制器navigation controller 的 navigation bar 可见且不透明，则navigation controller会布置最前面的视图控制器的视图，使其顶部边缘邻接导航栏的底部。在这种情况下，此属性的值为0。
> > > * 不使用约束的情况下使用topLayoutGuide，即请获取topLayoutGuide相对于包含视图顶部边界的位置。
> > >
> > > * ```
> > >   //view controller subclass中的使用方式
> > >   //只能在次获取，其他地方获取不到
> > >   - (void) viewDidLayoutSubviews {
> > >       CGRect viewBounds = self.view.bounds;
> > >       CGFloat topBarOffset = self.topLayoutGuide.length;
> > >   }
> > >
> > >   //view subclass中的使用方式
> > >   - (void) layoutSubviews {
> > >       [super layoutSubviews]; 
> > >       // You must call super here or the system raises an exception
> > >
> > >       CGRect bounds = self.bounds;
> > >       CGFloat topBarOffset = myVCReference.topLayoutGuide.length;
> > >   }
> > >   ```
> >
> > **bottomLayoutGuide**
> >
> > > 与topLayoutGuide类似详见官方文档
>
>
>
> **iOS11**
>
> > ```
> > @interface UIView(UIViewHierarchy)
> > /* The top of the safeAreaLayoutGuide indicates the unobscured top edge of the view (e.g, not behind
> >  the status bar or navigation bar, if present). Similarly for the other edges.
> >  */
> > @property(nonatomic,readonly,strong) UILayoutGuide *safeAreaLayoutGuide API_AVAILABLE(ios(11.0),tvos(11.0));
> > ...
> > @end
> > ```

#### 

* #### 参考文献：

> [https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/index.html\#//apple\_ref/doc/uid/TP40010853-CH7-SW1](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/index.html#//apple_ref/doc/uid/TP40010853-CH7-SW1)
>
> [Debugging iOS AutoLayout Issues](https://staxmanade.com/2015/06/debugging-ios-autolayout-issues/)
>
> [https://stackoverflow.com/questions/26389273/how-to-trap-on-uiviewalertforunsatisfiableconstraints](https://stackoverflow.com/questions/26389273/how-to-trap-on-uiviewalertforunsatisfiableconstraints)



