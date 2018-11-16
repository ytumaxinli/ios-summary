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

[https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/WorkingwithScrollViews.html\#//apple\_ref/doc/uid/TP40010853-CH24-SW1](#)

