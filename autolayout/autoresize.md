* #### **文章摘要**

> 1. 常见属性说明
>    2.

#### 常见属性

> **translatesAutoresizingMaskIntoConstraints**
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
> **autoresizesSubviews**
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
> **autoresizingMask**
>
> > An integer bit mask that determines how the receiver resizes itself when its superview’s bounds change.
> >
> > ## Declaration
> >
> > ## 
> >
> > When a view’s bounds change, that view automatically resizes its subviews according to each subview’s autoresizing mask. You specify the value of this mask by combining the constants described in[`UIViewAutoresizing`](https://developer.apple.com/documentation/uikit/uiviewautoresizing?language=objc)using the C bitwise OR operator. Combining these constants lets you specify which dimensions of the view should grow or shrink relative to the superview. The default value of this property is`UIViewAutoresizingNone`, which indicates that the view should not be resized at all.

@property\(nonatomic\)UIViewAutoresizingautoresizingMask; // simple resize. default is UIViewAutoresizingNone

参考

[Instance Property translatesAutoresizingMaskIntoConstraints](https://developer.apple.com/documentation/uikit/uiview/1622572-translatesautoresizingmaskintoco?language=objc)

