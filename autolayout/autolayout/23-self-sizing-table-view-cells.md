只有当view controller在屏幕最前面时，它的topLayoutGuide属性才会发挥作用。它是一个顶部与屏幕顶部对齐，宽度与屏幕宽度相同的一个view。当视图内容从它的底部开始布局时可以避免被半透明或透明UIKit栏遮挡（例如状态或导航栏）。在使用NSLayoutConstraint是可以将topLayoutGuide作为约束的一个constraint item使用。如：  
\[NSLayoutConstraint constraintWithItem:self.collectionView attribute:NSLayoutAttributeTop relatedBy:NSLayoutRelationLessThanOrEqual toItem:self.topLayoutGuide attribute:NSLayoutAttributeBottom multiplier:1 constant:0\];

topLayoutGuide的值可以通过调用self.topLayoutGuide.length获得。这个length由视图控制器或其封闭的容器视图控制器（navigation 或 tabbar controller）进行约束。具体如下：

1 没有被包裹的view controller，这个属性指的是status bar\(如果status bar显示的情况下\)的底部或者是view controller's view的上边缘。

2 被包裹的view controller不约束这个属性，相反包裹view controller来约束这个属性用来指示：

2.1  navigation bar的底部，如果navigation bar可见

2.2 status bar的底部，如果只用status bar可见

2.3 view controller's view上边缘，如果status bar 、navigation bar均可见

3 如果包裹控制器navigation controller 的 navigation bar 导航栏可见且不透明，则navigation controller会布置最前面的视图控制器的视图，使其顶部边缘邻接导航栏的底部。在这种情况下，此属性的值为0。

可以通过在[viewDidLayoutSubviews](apple-reference-documentation://hcUiuFufhX)的方法实现中来查询这个属性，比如用代码的方式想设置一个button的上边缘在top layout guide下20 points，使用代码如下

```
[button setTranslatesAutoresizingMaskIntoConstraints: NO];
id topGuide = myViewController.topLayoutGuide;
NSDictionary *viewsDictionary = NSDictionaryOfVariableBindings (button, topGuide);
[myViewController.view addConstraints:
    [NSLayoutConstraint constraintsWithVisualFormat: @"V:[topGuide]-20-[button]"
                                                 options: 0
                                                 metrics: nil
                                                   views: viewsDictionary]];
[self.view layoutSubviews]; // You must call this method here or the system raises an exception
```



不使用约束的情况下使用topLayoutGuide，即请获取topLayoutGuide相对于包含视图顶部边界的位置。

```
//view controller subclass中的使用方式
//只能在次获取，其他地方获取不到
- (void) viewDidLayoutSubviews {
    CGRect viewBounds = self.view.bounds;
    CGFloat topBarOffset = self.topLayoutGuide.length;
}
```

//view subclass中的使用方式

```
- (void) layoutSubviews {
    [super layoutSubviews]; 
    // You must call super here or the system raises an exception

    CGRect bounds = self.bounds;
    CGFloat topBarOffset = myVCReference.topLayoutGuide.length;
}
```



