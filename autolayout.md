* #### 点和像素

> 1. 在用户眼中，屏幕是由无数个像素组成的。像素越多，屏幕越清晰。
> 2. 在开发者眼中，屏幕是由无数个点组成的。点又是由像素组成的。像素越多，屏幕越清晰。在开发过程中我们经常使用的 frame、bounds、center等属性，设置其实是点。如下图在非retain屏幕上1个点就是1个像素，而在retain屏幕上1个点由2x2个像素组成。![](/assets/屏幕快照 2018-11-14 下午1.34.17.png)



* #### scale

> The natural scale factor associated with the screen.
>
> **Declaration**
>
> @property\(nonatomic, readonly\) CGFloat scale;
>
> **Discussion**
>
> This value reflects the scale factor needed to convert from the default logical coordinate space into the device coordinate space of this screen. The default logical coordinate space is measured using points. For Retina displays, the scale factor may be`3.0`or`2.0`and one point can represented by nine or four pixels, respectively. For standard-resolution displays, the scale factor is`1.0`and one point equals one pixel.



