

* #### 简介\(屏幕适配发展史\)

1. iOS程序UI布局方式技术发展。写固定值-&gt; autoresizingMask -&gt; autolayout-&gt; sizeClasses
2. iPhone1-iPhone4s时代屏幕的尺寸固定为\(320,480\) ,我们只需要简单计算一下相对位置就好了
3. iphone5-iphone5s时代屏幕的尺寸变了\(320,568\)这时AutoresizingMask派上了用场\(为什么不用Autolayout?因为还要支持ios5\)
4. iphone6时代屏幕的宽度也发生了变化,终于是时候抛弃AutoresizingMask改用Autolayout了\(1.不用支持ios5了2.相对于屏幕适配的多样性来说autoresizingMask也已经过时了\)
5. 直到iphone6发布之后我知道使用Autolayout势在必行了

* #### 点和像素

> 1. 在用户眼中，屏幕是由无数个像素组成的。像素越多，屏幕越清晰。
> 2. 在开发者眼中，屏幕是由无数个点组成的。点又是由像素组成的。像素越多，屏幕越清晰。在开发过程中我们经常使用的 frame、bounds、center等属性，设置其实是点。如下图在非retain屏幕上1个点就是1个像素，而在retain屏幕上1个点由2x2个像素组成。![](/assets/屏幕快照 2018-11-14 下午1.34.17.png)

* #### 屏幕比例因子scale

> The natural scale factor associated with the screen.
>
> **Declaration**
>
> @property\(nonatomic, readonly\) CGFloat scale;
>
> **Discussion**
>
> This value reflects the scale factor needed to convert from the default logical coordinate space into the device coordinate space of this screen. `The default logical coordinate space is measured using points. For Retina displays, the scale factor may be 3.0 or 2.0 and one point can represented by 9 or 4 pixels, respectively.` For standard-resolution displays, the scale factor is`1.0`and one point equals one pixel.

我们可以通过逻辑坐标空间到设备坐标空间的比例因子scale来进行,点与像素的转化。point\*scale\*scale = pixel

