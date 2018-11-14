* #### 简介

> Autolayout的功能比Autoresizing强大很多，Autolayout解决任何控件之间的相对关系问题。Autolayout的2个核心概念
>
> * 约束: 通过给控件添加约束,来决定控件的位置和尺寸
> * 参照: 在添加约束时,是依照谁来添加\(可以是父控件或者兄弟控件\)

* #### AutoLayout

> **自动布局的核心计算公式**
>
> obj1.property1 =（obj2.property2 \* multiplier）+ constant value
>
> **添加约束的规则  
> **对于两个同层级view之间的约束关系，添加到它们的父view上
>
> ![](/assets/image8.jpeg)
>
> 对于两个不同层级view之间的约束关系，添加到他们最近的共同父view上
>
> ![](/assets/image9.jpeg)
>
> 对于有层次关系的两个view之间的约束关系，添加到层次较高的父view上
>
> ![](/assets/image10.jpeg)



