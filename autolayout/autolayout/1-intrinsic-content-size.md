* #### 简介

> ```
> 有些视图具有给定当前内容的自然大小。这被称为它们的固有内容大小。
> some views have a natural size given their current content.
> ```

* #### Intrinsic content size for common controls

> | View | Intrinsic content size |
> | :--- | :--- |
> | UIView and NSView | No intrinsic content size. |
> | Sliders | Defines only the width \(iOS\).Defines the width, the height, or both—depending on the slider’s type \(OS X\). |
> | Labels, buttons, switches, and text fields | Defines both the height and the width. |
> | Text views and image views | Intrinsic content size can vary. |
>
> **详细介绍**
>
> 1. UILabel 和 UIButton 的固有尺寸是根据他们的显示内容和字体大小确定的
> 2. UIImageView的固有尺寸是根据它设置\(注意是设置\)的图片尺寸决定的。
>    > 2.1 当设置一个空图片的时候，它没有固有尺寸  
>    > 2.2 当设置了它的宽度约束，使其放置一个大尺寸图片且是设置其内容显示方式为等比缩放`[_imgView setContentMode:UIViewContentModeScaleAspectFit]`;此时它的高度固有尺寸仍为未缩放前的图片高度。
> 3. UITextView的固有尺寸取决于其内容、是否启用滚动以及应用于视图的其他约束。
>    > 3.1 如果UITextView设置scrollEnabled=YES,怎没有固有尺寸  
>    > 3.2 如果禁止scrollEnabled。
>    >
>    > > 3.2.1 默认情况下，视图的固有尺寸根据文本的大小计算的，没有任何换行。  
>    > > 3.2.2 如果文本中没有返回值，则计算将内容布局为一行文本所需的高度和宽度。  
>    > > 3.2.3 如果添加约束以指定视图的宽度，则内在内容大小将定义在给定宽度的情况下显示文本所需的高度

* #### AutoLayout 通过每个维度建一对约束来表示Intrinsic content size

> **CHCR**
>
> > Content Hugging将视图向内拉，使其紧贴内容不会被拉伸。优先级为UILayoutPriorityDefaultLow 250
> >
> > Compression resistance 向外推动视图，使得它不会夹住内容物不会被压缩。优先级为UILayoutPriorityDefaultHigh 750
> >
> > ![](/assets/屏幕快照 2018-11-16 下午5.06.21.png)
>
>
>
> **固有尺寸设置优先级**
>
> > 抗拉伸优先级为250，抗压缩优先级为750。也可以对此进行修改。
> >
> > ```
> > - (void)setContentHuggingPriority:(UILayoutPriority)priority forAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);
> >
> > - (void)setContentCompressionResistancePriority:(UILayoutPriority)priority forAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);
> > ```



