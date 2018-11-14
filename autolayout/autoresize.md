* #### **文章摘要**

> 1. **常见属性说明**
> 2. **使用示例**
> 3. **参考文献**

* #### 常见属性

> * **translatesAutoresizingMaskIntoConstraints**
>
> > A Boolean value that determines whether the view’s autoresizing mask is translated into Auto Layout constraints.
> >
> > **Declaration**
> >
> > ```
> > @property(nonatomic) BOOL translatesAutoresizingMaskIntoConstraints;
> > ```
> >
> > ##### Discussion
> >
> > Note that the autoresizing mask constraints fully specify the view’s size and position; therefore, you cannot add additional constraints to modify this size or position without introducing conflicts.
> >
> > ** If you want to use Auto Layout to dynamically calculate the size and position of your view, you must set this property to**`NO`**, and then provide a non ambiguous, nonconflicting set of constraints for the view.**
> >
> > By default, the property is set to`YES`for any view you programmatically create.
>
>
> * **autoresizesSubviews**
>
> > A Boolean value that determines whether the receiver automatically resizes its subviews when its bounds change.
> >
> > **Declaration**
> >
> > ```
> > @property(nonatomic) BOOL autoresizesSubviews;
> > ```
> >
> > **Discussion**
> >
> > When set to`YES`, the receiver adjusts the size of its subviews when its bounds change. The default value is`YES`.
>
>
> * **autoresizingMask**
>
> > An integer bit mask that determines how the receiver resizes itself when its superview’s bounds change.
> >
> > **Declaration**
> >
> > ```
> > @property(nonatomic) UIViewAutoresizing autoresizingMask
> > ```
> >
> > When a view’s bounds change, that view automatically resizes its subviews according to each subview’s autoresizing mask. You specify the value of this mask by combining the constants described in[`UIViewAutoresizing`](https://developer.apple.com/documentation/uikit/uiviewautoresizing?language=objc)using the C bitwise OR operator. Combining these constants lets you specify which dimensions of the view should grow or shrink relative to the superview. The default value of this property is`UIViewAutoresizingNone`, which indicates that the view should not be resized at all.
> >
> > When more than one option along the same axis is set, the default behavior is to distribute the size difference proportionally among the flexible portions. The larger the flexible portion, relative to the other flexible portions, the more it is likely to grow. For example, suppose this property includes the[`UIViewAutoresizingFlexibleWidth`](https://developer.apple.com/documentation/uikit/uiviewautoresizing/uiviewautoresizingflexiblewidth?language=objc)and[`UIViewAutoresizingFlexibleRightMargin`](https://developer.apple.com/documentation/uikit/uiviewautoresizing/uiviewautoresizingflexiblerightmargin?language=objc)constants but does not include the`UIViewAutoresizingFlexibleLeftMargin`constant, thus indicating that the width of the view’s left margin is fixed but that the view’s width and right margin may change. Thus, the view appears anchored to the left side of its superview while both the view width and the gap to the right of the view increase.
> >
> > If the autoresizing behaviors do not offer the precise layout that you need for your views, you can use a custom container view and override its[`layoutSubviews`](https://developer.apple.com/documentation/uikit/uiview/1622482-layoutsubviews?language=objc)method to position your subviews more precisely.
>
>
> * **UIViewAutoresizing**
>
> ```
> typedefNS_OPTIONS(NSUInteger, UIViewAutoresizing) {
>  UIViewAutoresizingNone  = 0,                       //不自动调整
>  UIViewAutoresizingFlexibleLeftMargin  = 1<<0,      //自动调整与superView左边的距离,与superView右边的距离不变
>  UIViewAutoresizingFlexibleWidth = 1<<1,            //自动调整自己的宽度，与superView左边和右边的距离不变
>  UIViewAutoresizingFlexibleRightMargin = 1<<2,      //自动调整与superView的右边距离，与superView左边的距离不变
>  UIViewAutoresizingFlexibleTopMargin = 1<<3,        //自动调整与superView顶部的距离，与superView底部的距离不变
>  UIViewAutoresizingFlexibleHeight  = 1<<4,          //自动调整自己的高度，与superView顶部和底部的距离不变
>  UIViewAutoresizingFlexibleBottomMargin = 1<<5      //自动调整与superView底部的距离，与superView顶部的距离不变
> };
> ```



* #### 参考文献

> [Instance Property translatesAutoresizingMaskIntoConstraints](https://developer.apple.com/documentation/uikit/uiview/1622572-translatesautoresizingmaskintoco?language=objc)
>
> [iOS开发－自动布局之autoresizingMask使用详解](https://www.cnblogs.com/GarveyCalvin/p/4165151.html)



