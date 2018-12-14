**Class**

#### [NSKeyedArchiver](https://developer.apple.com/documentation/foundation/nskeyedarchiver?language=objc)

**简介**

> 将对象的数据存储到由密钥引用的归档的编码器。
>
> A coder that stores an object's data to an archive referenced by keys.

**Declaration**

> @interface NSKeyedArchiver : NSCoder

**Overview**

> NSKeyedArchiver是NSCoder抽象类的实现子类，提供了一种将对象（和标量值）编码为独立于体系结构的格式的方法，该格式可以存储在文件中。归档一组对象时，每个对象的类信息和实例变量都将写入归档。对应的类NSKeyedUnarchiver对存档中的数据进行解码，并创建一组与原始集相当的对象。
>
> 密钥存档与非密钥存档的不同之处在于，编码到存档中的所有对象和值都是给定的名称或密钥。解码非密钥存档时，必须按照编码它们的相同顺序对值进行解码。解码密钥存档时，因为值是按名称请求的，所以可以不按顺序解码值，或者根本不解码。因此，密钥存档为前向和后向兼容性提供了更好的支持。
>
> 赋予编码值的键必须仅在当前编码对象的范围内是唯一的。键控存档是分层的，因此对象A用于编码其实例变量的键不会与对象B使用的键冲突，即使A和B是同一类的实例也是如此。但是，在单个对象中，子类使用的键可能与其超类中使用的键冲突。
>
> NSArchiver对象可以将归档数据写入您提供的文件或可变数据对象（NSMutableData的实例）。



