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
> > > 2.2 设置dummy view的height、width属性与承载scollview的父view相同
> >
> > 设置其leading、 trailing、 top 、 bottom与scrollView的
> >
> > [Calculating contentSize for UIScrollView when using Auto Layout](https://stackoverflow.com/questions/38948904/calculating-contentsize-for-uiscrollview-when-using-auto-layout)
>
> **Use  layout group to contain the scroll view’s content**
>
> > 要根据内容的内在大小设置高度，您必须拥有从内容视图的上边缘到下边缘的不间断的约束和视图链。 同样，要设置宽度，您必须具有从内容视图的前缘到后缘的完整的约束和视图链。`To set the height based on the intrinsic size of your content, you must have an unbroken chain of constraints and views stretching from the content view’s top edge to its bottom edge. Similarly, to set the width, you must have an unbroken chain of constraints and views from the content view’s leading edge to its trailing edge`

#### 

[https://stackoverflow.com/questions/38948904/calculating-contentsize-for-uiscrollview-when-using-auto-layout](https://stackoverflow.com/questions/38948904/calculating-contentsize-for-uiscrollview-when-using-auto-layout)

[https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/WorkingwithScrollViews.html\#//apple\_ref/doc/uid/TP40010853-CH24-SW1](#)

