#### 目的

> 1.保证每一个安装到 iOS 上的 APP 都是经过苹果官方授权未被篡改的
>
> 2.控制app权限（设备列表，push等）

#### **概念**

> 1. 证书：内容是公钥或私钥，由其他机构对其签名组成的数据包。
> 2. **Entitlements**：包含了 App 权限开关列表。
> 3. **CertificateSigningRequest**：本地公钥。
> 4. **p12**：本地私钥，可以导入到其他电脑。
> 5. **Provisioning Profile**：包含了 证书 / Entitlements 等数据，并由苹果后台私钥签名的数据包。

#### 流程

> ![](/assets/codesigning.png)①在你的 Mac 开发机器生成一对公私钥，这里称为公钥L，私钥L。L:Local
>
> ②苹果自己有固定的一对公私钥，私钥在苹果后台，公钥在每个 iOS 设备上。这里称为公钥A，私钥A。A:Apple
>
> ③把公钥 L 传到苹果后台，用苹果后台里的私钥 A 去签名公钥 L。得到一份数据包含了公钥 L 以及其签名，把这份数据称为证书。
>
> ④在苹果后台申请 AppID，配置好设备 ID 列表和 APP 可使用的权限，再加上第③步的证书，组成的数据用私钥 A 签名，把数据和签名一起组成一个 Provisioning Profile 文件，下载到本地 Mac 开发机。
>
> ⑤在开发时，编译完一个 APP 后，用本地的私钥 L 对这个 APP 进行签名，同时把第③步得到的cer证书和第④步得到的描述文件\(embedded.mobileprovision\)一起打包进 APP 里，把 APP 安装到手机上。
>
> ⑥在安装时，1.通过系统内置的公钥 A解密证书签名得到摘要串，并通过证书中描述的摘要算法得到新的摘要与解密出的摘要串对比，相同则公钥经过苹果授权并且没有被修改 2.通过内置公钥 A解密对比描述文件的摘要串，鉴别描述文件是否被修改。
>
> ⑦经过⑥的验证后，从证书中取出公钥 L 验证APP签名，从描述文件中取出Entitlements验证设备列表、权限开关等。

#### 操作

> 第 1 步对应的是 keychain 里的 “从证书颁发机构请求证书”,这里就本地生成了一对公私钥,保存的 CertificateSigningRequest 里面就包含公钥,私钥保存在本地电脑里。
>
> 第 2 步苹果处理，不用管。
>
> 第 3 步向苹果申请对应把 CSR 传到苹果后台生成证书.把证书下载到本地.这时本地有两个证书.一个是第 1 步生成的私钥,一个是这里下载回来的证书,keychain 会把这两个证书关联起来,因为他们公私钥是对应的,在XCode选择下载回来的证书时,实际上会找到 keychain 里对应的私钥去签名.这里私钥只有生成它的这台 Mac 有,如果别的 Mac 也要编译签名这个 App 怎么办?答案是把私钥导出给其他 Mac 用,在 keychain 里导出私钥,就会存成 .p12 文件,其他 Mac 打开后就导入了这个私钥.
>
> ![](https://mmbiz.qpic.cn/mmbiz/yoM0qNN7RwbMzODS0N8ZbEkhU1pcPTUdTPicxWImhsZeftvsZaibHHM23kuUyHL7kEgiaicaHmnqbaaRrM9ALK7jgg/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
>
> 第 4 步都是在苹果网站上操作，配置 AppID / 权限 / 设备等，最后下载 Provisioning Profile 文件。
>
> 第 5-7 步 XCode 会通过第 3 步下载回来的证书（存着公钥），在本地找到对应的私钥（第一步生成的），用本地私钥去签名 App，并把 Provisioning Profile 文件命名为 embedded.mobileprovision 一起打包进去。所以任何本地调试的APP,都会有一个embedded.mobileprovision\(描述文件\)从App Store下载的没有.

#### **APP签名的数据**

> **这里对 App 的签名数据保存分两部分**
>
> * 1.Mach-O 可执行文件会把签名直接写入文件里
>
> ![](https://mmbiz.qpic.cn/mmbiz/yoM0qNN7RwbMzODS0N8ZbEkhU1pcPTUd3bfyMmxjjbNCFMRJjAPTMBSbvZibtETGibvibI2npEs5ge4czZgiciaMGeg/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
>
> **MachOView查看**
>
> * 2.其他资源文件则会保存在 \_CodeSignature 目录下在APP包里。
>
> ![](https://mmbiz.qpic.cn/mmbiz/yoM0qNN7RwbMzODS0N8ZbEkhU1pcPTUdJaBuFz8FhUdddZ7m6v8Tj3ZBt9Qqic1P9KcjPrwp68iapMosZEfiarHgg/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

#### 非对称加密

> 通常我们说的签名就是数字签名，它是基于非对称加密算法实现的。对称加密是通过同一份密钥加密和解密数据，而非对称加密则有两份密钥，分别是公钥和私钥，用公钥加密的数据，要用私钥才能解密，用私钥加密的数据，要用公钥才能解密。
>
> 简单说一下常用的非对称加密算法 RSA 的数学原理，理解简单的数学原理，就可以理解非对称加密是怎么做到的，为什么会是安全的：
>
> 1. 选两个质数`p`和`q`，相乘得出一个大整数`n`，例如 p=61，q=53，n=pq=3233
> 2. 选 1-n 间的随便一个质数`e`，例如 e = 17
> 3. 经过一系列数学公式，算出一个数字`d`，满足：
>    a. 通过`n`和`e`这两个数据一组数据进行数学运算后，可以通过 n 和 d 去反解运算，反过来也可以。
>    b. 如果只知道`n`和`e`，要推导出`d`，需要知道`p`和`q`，也就是要需要把 n 因数分解。
>
> 上述的`(n,e)`这两个数据在一起就是公钥，`(n,d)`这两个数据就是私钥，满足用公钥加密，私钥解密，或反过来公钥加密，私钥解密，也满足在只暴露公钥（只知道`n`和 e）的情况下，要推导出私钥`(n,d)`，需要把大整数`n`因数分解。目前因数分解只能靠暴力穷举，而n数字越大，越难以用穷举计算出因数`p`和`q`，也就越安全，当`n`大到二进制 1024 位或 2048 位时，以目前技术要破解几乎不可能，所以非常安全。
>
> 若对数字`d`是怎样计算出来的感兴趣，可以详读这两篇文章：RSA 算法原理[（一）](http://www.ruanyifeng.com/blog/2013/06/rsa_algorithm_part_one.html)[（二）](http://www.ruanyifeng.com/blog/2013/07/rsa_algorithm_part_two.html)

#### 

#### **参考文献**

> http://wereadteam.github.io/2017/03/13/Signature/
>
> https://mp.weixin.qq.com/s?\_\_biz=MzU5OTcyMzc5MQ==&mid=2247483771&idx=1&sn=aeef3ae164197b472aa5c3b92e85366e&chksm=feb1db1bc9c6520dec9e391e124f5d5482298906391dfbeaaaa57e5ce9c5a52b06786bf3522c&mpshare=1&scene=23&srcid=1031Qt2WSUR1flo9VmNqhuMX%23rd
>
> https://mp.weixin.qq.com/s?\_\_biz=MzU5OTcyMzc5MQ==&mid=2247483775&idx=1&sn=d16071e1b95adbffa9f13763234b389b&chksm=feb1db1fc9c652094112df723eae9b371ece321c7573381a38223a4b3516d570de3e2ac1f619&mpshare=1&scene=23&srcid=102955kjq0NWUyIN9QN1yUk5%23rd
>
> https://developer.apple.com/library/archive/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html\#//apple\_ref/doc/uid/TP40005929-CH1-SW1





