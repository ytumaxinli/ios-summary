**Crash 产生原因**

> KVO的被观察者dealloc时仍然注册着KVO导致的crash **（iOS8 上存在，iOS12未发现）**
>
> > ![](http://mmbiz.qpic.cn/mmbiz_png/ibnDKD3ktAoYJIS62nicicpVqYtEtLgf7OKDRmEQLqibW6RyJEiaKv78g0YogJddUPdU8hx8ZCQXSQibhBREpBPBybGw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
>
> 添加KVO重复添加观察者或重复移除观察者（KVO注册观察者与移除观察者不匹配）导致的crash，见下图
>
> > ![](http://mmbiz.qpic.cn/mmbiz_png/ibnDKD3ktAoYJIS62nicicpVqYtEtLgf7OKsDiaNKudLEnhnw01ZePzz0we9jAtiaWm8adlrPRlNOPUJaqT5pnZiaQ3w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**防护方案**

>



