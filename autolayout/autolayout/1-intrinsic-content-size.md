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
> **固有尺寸设置优先级**
>
> > 抗拉伸优先级为250，抗压缩优先级为750。也可以对此进行修改。
> >
> > ```
> > - (void)setContentHuggingPriority:(UILayoutPriority)priority forAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);
> > - (void)setContentCompressionResistancePriority:(UILayoutPriority)priority forAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);
> > ```

* #### 示例

> ```
> //两个水平布局的label，两边间隔分别是12，中间间隔为8（懂意思就行）
>
> UILabel *label1 = [[UILabel alloc] initWithFrame:CGRectZero];
> label1.backgroundColor = [UIColor redColor];
> label1.text = @"我是标题";
> [self.view addSubview:label1];
>  [label1 mas_makeConstraints:^(MASConstraintMaker *make) {
>     make.centerY.equalTo(self.view);
>     make.left.equalTo(@(12));
>  }];
>     
> UILabel *label2 = [[UILabel alloc] initWithFrame:CGRectZero];
> label2.backgroundColor = [UIColor redColor];
> label2.text = @"我是描述";
> [self.view addSubview:label2];
> [label2 mas_makeConstraints:^(MASConstraintMaker *make) {
>     make.centerY.equalTo(label1);
>     make.left.equalTo(label1.mas_right).offset(8);
>     make.right.equalTo(self.view).offset(-12);
> }];
>
> //通过设置抗拉伸优先级，改变被拉伸的lable
> [label1 setContentHuggingPriority:UILayoutPriorityRequired
>                           forAxis:UILayoutConstraintAxisHorizontal];
> [label2 setContentHuggingPriority:UILayoutPriorityDefaultLow
>                           forAxis:UILayoutConstraintAxisHorizontal];
> ```
>
> 如果不添加任何约束是图一这样显示的。\(此时的约束是一个Ambiguous Layouts\)
>
> ![](/assets/2701344-43751df97caa3b44.png)
>
> 如果我们的需求是label1正常显示，拉伸label2呢，或者说**label2的内容紧跟着label1的内容显示**。只需要这样做：
>
> ![](/assets/2701344-cf1a2c1f96df59e8.png)

* #### [其它常见示例](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/ViewswithIntrinsicContentSize.html#//apple_ref/doc/uid/TP40010853-CH13-SW1)

> **1 Dynamic Height Label and Text Field**
>
> Name Label和Name Text Field的底部对齐，并它们中height较高的控件的上边缘为基准距父view的上边缘向下偏移20
>
> > **约束展示**![](/assets/dynamic_height_label_and_text_field_2x.png)**约束伪代码**
> >
> > ```
> > Name Label.Leading = Superview.LeadingMargin
> > Name Text Field.Trailing = Superview.TrailingMargin
> > Name Text Field.Leading = Name Label.Trailing + Standard
> > Name Label.Top >= Top Layout Guide.Bottom + 20.0
> > Name Label.Top = Top Layout Guide.Bottom + 20.0 (Priority 249)
> > Name Text Field.Top >= Top Layout Guide.Bottom + 20.0
> > Name Text Field.Top = Top Layout Guide.Bottom + 20.0 (Priority 249)
> > Name label.Baseline = Name Text Field.Baseline
> > ```
> >
> > **Discussion**
> >
> > This recipe uses a pair of constraints for each control. A required, greater-than-or-equal constraint defines the minimum distance between that control and the layout guide, while an optional constraint tries to pull the control to exactly 20.0 points from the layout guide.
> >
> > Both constraints are satisfiable for the taller constraint, so the system places it exactly 20.0 points from the layout guide. However, for the shorter control, only the minimum distance is satisfiable. The other constraint is ignored. This lets the Auto Layout system dynamically recalculate the layout as the height of the controls change at runtime.
>
> **2 Fixed Height Columns**
>
> 水平方向上三个lable左右对齐，width以内容最长的Lable为准。text field占据剩余空间
>
> > **约束展示**![](/assets/fixed_height_columns_2x.png)**约束伪代码**
> >
> > ```
> > First Name Label.Leading = Superview.LeadingMargin
> >
> > Middle Name Label.Leading = Superview.LeadingMargin
> >
> > Last Name Label.Leading = Superview.LeadingMargin
> >
> > First Name Text Field.Leading = First Name Label.Trailing + Standard
> >
> > Middle Name Text Field.Leading = Middle Name Label.Trailing + Standard
> >
> > Last Name Text Field.Leading = Last Name Label.Trailing + Standard
> >
> > First Name Text Field.Trailing = Superview.TrailingMargin
> >
> > Middle Name Text Field.Trailing = Superview.TrailingMargin
> >
> > Last Name Text Field.Trailing = Superview.TrailingMargin
> >
> > First Name Label.Baseline = First Name Text Field.Baseline
> >
> > Middle Name Label.Baseline = Middle Name Text Field.Baseline
> >
> > Last Name Label.Baseline = Last Name Text Field.Baseline
> >
> > First Name Text Field.Width = Middle Name Text Field.Width
> >
> > First Name Text Field.Width = Last Name Text Field.Width
> >
> > First Name Text Field.Top = Top Layout Guide.Bottom + 20.0
> >
> > Middle Name Text Field.Top = First Name Text Field.Bottom + Standard
> >
> > Last Name Text Field.Top = Middle Name Text Field.Bottom + Standard
> > ```
> >
> > **Discussion**
> >
> > Additionally, because a label’s compression resistance is greater than its content hugging, all the labels prefer to be stretched rather than squeezed
>
> **3 Dynamic Height Columns**
>
> > **The goals of this recipe include:**
> >
> > > The trailing edge of the labels are aligned, based on the length of the longest label.
> > >
> > > The text fields are the same width, and their leading and trailing edges are aligned.
> > >
> > > The text fields expand to fill all the remaining space in the superview.
> > >
> > > The height between rows are based on the tallest element in the row.
> > >
> > > Everything is dynamic, so if the font size or label text changes, the layout automatically updates.
> >
> > **约束展示**![](/assets/dynamic_columns_2x.png)**约束伪代码**
> >
> > ```
> > First Name Label.Leading = Superview.LeadingMargin
> > Middle Name Label.Leading = Superview.LeadingMargin
> > Last Name Label.Leading = Superview.LeadingMargin
> > First Name Text Field.Leading = First Name Label.Trailing + Standard
> > Middle Name Text Field.Leading = Middle Name Label.Trailing + Standard
> > Last Name Text Field.Leading = Last Name Label.Trailing + Standard
> > First Name Text Field.Trailing = Superview.TrailingMargin
> > Middle Name Text Field.Trailing = Superview.TrailingMargin
> > Last Name Text Field.Trailing = Superview.TrailingMargin
> > First Name Label.Baseline = First Name Text Field.Baseline
> > Middle Name Label.Baseline = Middle Name Text Field.Baseline
> > Last Name Label.Baseline = Last Name Text Field.Baseline
> > First Name Text Field.Width = Middle Name Text Field.Width
> > First Name Text Field.Width = Last Name Text Field.Width
> > First Name Label.Top >= Top Layout Guide.Bottom + 20.0
> > First Name Label.Top = Top Layout Guide.Bottom + 20.0 (Priority 249)
> > First Name Text Field.Top >= Top Layout Guide.Bottom + 20.0
> > First Name Text Field.Top = Top Layout Guide.Bottom + 20.0 (Priority 249)
> > Middle Name Label.Top >= First Name Label.Bottom + Standard
> > Middle Name Label.Top = First Name Label.Bottom + Standard (Priority 249)
> > Middle Name Text Field.Top >= First Name Text Field.Bottom + Standard
> > Middle Name Text Field.Top = First Name Text Field.Bottom + Standard (Priority 249)
> > Last Name Label.Top >= Middle Name Label.Bottom + Standard
> > Last Name Label.Top = Middle Name Label.Bottom + Standard (Priority 249)
> > Last Name Text Field.Top >= Middle Name Text Field.Bottom + Standard
> > Last Name Text Field.Top = Middle Name Text Field.Bottom + Standard (Priority 249)
> > ```
>
> **4 Two Buttons with Equal Spacing**
>
> the buttons’ widths are based on the longest title. If there’s enough space, the buttons are stretched only until they both match the intrinsic content size of the longer button. Any additional space is divided evenly around the buttons.
>
> > **约束展示**![](/assets/two_buttons_with_equal_spacing_2x.png)**约束伪代码**
> >
> > ```
> > Leading Dummy View.Leading = Superview.LeadingMargin
> > Short Button.Leading = Leading Dummy View.Trailing
> > Center Dummy View.Leading = Short Button.Trailing
> > Long Button.Leading = Center Dummy View.Trailing
> > Trailing Dummy View.Leading = Long Button.Trailing
> > Trailing Dummy View.Trailing = Superview.TrailingMargin
> > Bottom Layout Guide.Top = Leading Dummy View.Bottom + 20.0
> > Bottom Layout Guide.Top = Short Button.Bottom + 20.0
> > Bottom Layout Guide.Top = Center Dummy View.Bottom + 20.0
> > Bottom Layout Guide.Top = Long Button.Bottom + 20.0
> > Bottom Layout Guide.Top = Trailing Dummy View.Bottom + 20.0
> > Short Button.Leading >= Superview.LeadingMargin
> > Long Button.Leading >= Short Button.Trailing + Standard
> > Superview.TrailingMargin >= Long Button.Trailing
> > Leading Dummy View.Width = Center Dummy View.Width
> > Leading Dummy View.Width = Trailing Dummy View.Width
> > Short Button.Width = Long Button.Width
> > Leading Dummy View.Height = 0.0
> > Center Dummy View.Height = 0.0
> > Trailing Dummy View.Height = 0.0
> > ```
> >
> > **Discussion**
> >
> > In this recipe, you use dummy views to represent the empty space. These views are empty instances of the UIView class. In this recipe, they are given a 0-point height to minimize their effect on the view hierarchy.
> >
> > This recipe uses greater-than-or-equal constraints to set the minimum spacing around the buttons. Required constraints also guarantee that the buttons are always the same width, and the dummy views are also always the same width \(though, they can be a different width than the buttons\). The rest of the layout is largely managed by the button’s CHCR priorities. If there isn’t enough space, the dummy views collapse to a 0-point width, and the button’s divide the available space amongst themselves \(with the standard spacing between them\). As the available space increases, the buttons expand until they reach the larger button’s intrinsic width, then the dummy views begin to expand. The dummy views continue to expand to fill any remaining space.
>
> 其他
>
> [https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/ViewswithIntrinsicContentSize.html\#//apple\_ref/doc/uid/TP40010853-CH13-SW1](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/ViewswithIntrinsicContentSize.html#//apple_ref/doc/uid/TP40010853-CH13-SW1)

* #### 参考文献：

> [Masonry 和 布局优先级约束](https://www.jianshu.com/p/63e9765feb3f)
>
> [Anatomy of a Constraint](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/AnatomyofaConstraint.html#//apple_ref/doc/uid/TP40010853-CH9-SW1)



