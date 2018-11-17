* #### 简介

> 当AutoLayout作用于scroll views,不仅要设置scroll views的大小\(size\)、相对于父控件的位置\(position\)还要设置它的内容区域\(scroll view’s content area\)的尺寸。这些都可以使用AutoLayout来完成。
>
> ```
> When working with scroll views, you need to define both the size and position of the scroll view’s frame within its superview, and the size of the scroll view’s content area. All of these features can be set using Auto Layout.
> ```

* #### 约束分类

> **scroll views的约束可以分为两类，作用于scroll view’s frame上的约束和作用于scroll view’s content area上的约束。**
>
> **scroll view’s frame上的约束：**
>
> > * scroll view与它之外的视图之间的约束都作用于scroll view’s frame
> >
> > ```
> > Any constraints between the scroll view and objects outside the scroll view attach to the scroll view’s frame, just as with any other view
> > ```
> >
> > * 高度，宽度或中心之间的约束附作用于scroll view's frame。
> >
> > ```
> > Constraints between the height, width, or centers attach to the scroll view’s frame
> > ```
>
> * **content area上的约束**
>
> > scroll view的边缘或边距与其内容之间的约束作用于scroll view’s content area
> >
> > ```
> > Constraints between the edges or margins of the scroll view and its content attach to the scroll view’s content area
> > ```

* #### Use a dummy view or layout group to contain the scroll view’s content

> **Use a dummy view to contain the scroll view’s content**
>
> > 使用虚拟View的步骤如下：  
> > 1 创建scrollView使用leading、 trailing、 top 、 bottom、height and width等属性，确定scrollView的frame\(size、positon\)
> >
> > 2 不直接在scrollview上添加需要显示的UI控件。首先创建一个view添加到scrollView上，这个就是所说的dummy view 它用来承载所用用来显示的UI子控件的。其次在这个dummy view上添加以下约束。
> >
> > > 2.1 设置dummy view的Leading, trailing, top and bottom与scrollview相等
> > >
> > > 2.2 设置dummy view的height、width属性与承载scollview的父view相同\(可以视情况来进行设定具体值\),并将equal height约束的优先级设为低优先级（承载的UI子view有确定的Height或者有intrinsic size则可以调整dummy view的高度）。
> > >
> > > 2.3 如果所有子view在垂直方向上有确定的Height或者有intrinsic size，且构成了一个不间断的约束链。设置UI子view的最后一个元素bottom与dummy view的底部相同，来调整dummy view的高度。
> > >
> > > 总结：
> > >
> > > > 1 如果dummy view如果所有子view在垂直方向上有确定的Height或者有intrinsic size，且构成了一个不间断的约束链。dummy view的高度即scrollView的content size height,由**子views**决定。
> > > >
> > > > 2 如果没有完成的约束链，dummy view的高度即scrollView的content size height，由之前设置的低优先级height决定
> > > >
> > > > [Calculating contentSize for UIScrollView when using Auto Layout](#)
>
> **Use  layout group to contain the scroll view’s content**
>
> > 当scroll view的content view’s有intrinsic size和或者确定height时，可以通过从上边缘到下边缘不间断的约束和视图链，来确定content view的高度。设置宽度也是如此。
> >
> > ```
> > To set the height based on the intrinsic size of your content, you must have an unbroken chain of constraints and views stretching from the content view’s top edge to its bottom edge. Similarly, to set the width, you must have an unbroken chain of constraints and views from the content view’s leading edge to its trailing edge
> > ```
> >
> > **示例**
> >
> > 从\_ivTop与\_scvBase上边缘建立约束到\_ivBottom与_scvBase_下边缘建立约束，建立了一个不间断的约束链。由于\_ivTop、\_ivMiddle、\_ivBottom都有intrinsic size，因此确定了\_scvBase content view的高度
> >
> > ```
> > _scvBase = [[UIScrollView alloc] init];
> > [self.view addSubview:_scvBase];
> > //设置通过约束设置scrollview的frame
> > [_scvBase makeConstraints:^(MASConstraintMaker *make) {
> >     if(@available(iOS 11.0, *)){
> >         make.top.equalTo(self.view.safeAreaLayoutGuideTop);
> >         make.bottom.equalTo(self.view.safeAreaLayoutGuideBottom);
> >     }else {
> >         make.top.bottom.equalTo(self.view);
> >     }
> >     make.left.width.equalTo(self.view);
> > }];
> >
> > UIImage *imgTop = [UIImage imageNamed:@"LinviteTop"];
> > [_ivTop setBackgroundColor:[UIColor yellowColor]];
> > _ivTop = [[UIImageView alloc] initWithImage:imgTop];
> > [_scvBase addSubview:_ivTop];
> > [_ivTop makeConstraints:^(MASConstraintMaker *make) {
> >     //建立上边缘约束（_ivTop 有 intrinsic size）
> >     make.top.equalTo(self->_scvBase);
> >
> >         //确定scrollview content view width
> >     make.left.right.equalTo(self->_scvBase);
> >     make.width.equalTo(SCREEN_WIDTH);
> > }];
> >
> > UIImage *imgMiddle = [UIImage imageNamed:@"LinviteMiddle"];
> > [_ivMiddle setBackgroundColor:[UIColor yellowColor]];
> > _ivMiddle = [[UIImageView alloc] initWithImage:imgTop];
> > [_scvBase addSubview:_ivMiddle];
> > [_ivMiddle makeConstraints:^(MASConstraintMaker *make) {
> >     //不间断约束（_ivMiddle 有 intrinsic size）
> >     make.top.equalTo(self->_ivTop.bottom);
> >     make.left.equalTo(self->_ivTop);
> > }];
> >
> >
> > UIImage *imgBottom = [UIImage imageNamed:[@"LinviteBottom" stringByAppendingString:self.imgSuffix]];
> > _ivBottom = [[UIImageView alloc] initWithImage:imgBottom];
> > [_ivBottom setContentMode:UIViewContentModeScaleAspectFit];
> > [_scvBase addSubview:_ivBottom];
> > [_ivBottom makeConstraints:^(MASConstraintMaker *make) {
> >     //不间断约束（_ivBottom 有 intrinsic size）
> >     make.top.equalTo(self->_imgMiddle.bottom).offset(10);
> >     make.left.equalTo(self->_ivTop);    
> >     //建立下边缘约束
> >     make.bottom.equalTo(self->_scvBase.bottom);
> > }];
> > ```

#### 

[https://stackoverflow.com/questions/38948904/calculating-contentsize-for-uiscrollview-when-using-auto-layout](https://stackoverflow.com/questions/38948904/calculating-contentsize-for-uiscrollview-when-using-auto-layout)

[https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/WorkingwithScrollViews.html\#//apple\_ref/doc/uid/TP40010853-CH24-SW1](#)

