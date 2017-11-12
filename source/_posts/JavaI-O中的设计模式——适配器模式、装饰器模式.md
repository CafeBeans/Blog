---
title: JavaI/O体系及当中设计模式的运用
date: 2017-06-12 23:20:38
categories: 技术探究
tags:
- Java
- 设计模式
---

在朋友的推荐下，机缘巧合的发现了一个挺不错的知乎专栏[进击的Java新人](https://zhuanlan.zhihu.com/hinus)，觉得作者对一些Java概念与运用的理解很透彻，也通俗易懂，对我的Java学习很有帮助。看到装饰器模式与适配器模式[那一章节](https://zhuanlan.zhihu.com/p/24457041)的时候，还结合了JavaI/O流去讲解，给我回顾JavaI/O流提供了新的思路。于是稍微总结了一下JavaIO流，以及其中运用到的设计模式。
<!-- more -->
## 设计模式

先了解下在JavaIO中用到的适配器模式与装饰模式

### 适配器模式——协调互不兼容的结构

#### 介绍
适配器模式，属于结构型模式之一，用于协调互不兼容的结构。
在生活中，我们常常会碰到这样的情况，要给手机充电，得先找一个充电器来做电压的适配，否则就会烧掉。因此，适配器模式，就是在不改变现有对象结构的前提下，从中引入一个称为适配器的组件来协调双方，以相互支持。在手机，电源，充电器的关系中，充电器则作为适配器，毕竟我们不能更改手机工作电压和家庭标准电压嘛。
在软件开发过程中，同样会有各种互不兼容的接口和类库，我们就可以从中引入一个适配器，来使这些不兼容对象一起运作。

> 在适配器模式中，我们通过增加一个新的适配器类来将一个接口转换为客户希望的另一个接口以解决接口不兼容的问题，使得原本没有任何关系的类可以协同工作。
根据适配器类与适配者类的关系不同，适配器模式可分为对象适配器和类适配器两种，在对象适配器模式中，适配器与适配者之间是关联关系；在类适配器模式中，适配器与适配者之间是继承（或实现）关系。在实际开发中，对象适配器的使用频率更高

#### 对象适配器模式结构图
![](http://img.my.csdn.net/uploads/201302/28/1362066399_9469.jpg)

在上述结构图中，客户Client需要的是一个Target接口实现一个request()方法，然而现在只有Adaptee接口specificRequest()方法可以使用，因此需要一个适配器Adapter，作为转换器对Adaptee和Target进行适配，维护一个Adaptee对象，进而可以在适配器中调用该对象的方法。

结构图代码实现：
```
class Adapter extends Target {  
    private Adaptee adaptee; //维持一个对适配者对象的引用  

    public Adapter(Adaptee adaptee) {  
        this.adaptee=adaptee;  
    }  

    public void request() {  
        adaptee.specificRequest(); //转发调用  
    }  
}
```

仔细想想，我们能否在一个适配器里面适配多个适配者呢？当然可以，只需要在适配器里面维护多一个适配者对象引用就好了。也就相当于我们在充电器里面添加了一个高电压的插件，现在不仅可以选择充手机，还可以选择为大型电器持续供电了。

#### ReadMore
[适配器模式-Adapter Pattern](https://gof.quanke.name/%E9%80%82%E9%85%8D%E5%99%A8%E6%A8%A1%E5%BC%8F-Adapter%20Pattern.html)

### 装饰模式——扩展系统的功能
#### 介绍
装饰模式，结构型设计模式之一，用于扩展系统的功能。
作为技术控，有时候我们会经常自己升级电脑，给自己的电脑加固态什么的，这个时候，其实就是装饰模式的体现，在不改变一个对象本身功能的基础上给对象添加额外的新行为。而且装饰模式是一种比继承更好的技术，使用对象之间的关联关系取代类之间的继承关系，比继承更为灵活。

>装饰模式是一种用于替代继承的技术，它通过一种无须定义子类的方式来给对象动态增加职责，使用对象之间的关联关系取代类之间的继承关系。在装饰模式中引入了装饰类，在装饰类中既可以调用待装饰的原有类的方法，还可以增加新的方法，以扩充原有类的功能。

其实也非常好理解，就是可以动态为对象增加额外能力嘛。

#### 装饰模式结构图

![](http://my.csdn.net/uploads/201204/04/1333528185_7832.gif)

在装饰模式中，为了让系统具有更好的灵活性和可扩展性，通常会定义一个抽象装饰类，而将具体的装饰类作为它的子类.
而且，由于具体构件与抽象装饰类都实现相同的抽象构件，因此对于客户端而言，装饰前后的构件是透明的，客户端察觉不出差别（装饰前是Component，装饰后仍然是Component），这种情况下我们还可以对已经装饰过的对象多次进行装饰。

代码实现：
抽象装饰类：
```
class Decorator implements Component{

	//维持一个对抽象构件对象的引用
    private Component component;  

    //注入一个抽象构件类型的对象
    public Decorator(Component component){
        this.component=component;
    }

    public void operation(){
    	//调用原有业务方法
        component.operation();  
    }
}
```
具体装饰类：
```
class ConcreteDecorator extends Decorator{

    public ConcreteDecorator(Component  component){
        super(component);
    }

	public void operation(){
        super.operation();  //调用原有业务方法
        addedBehavior();  //调用新增业务方法
    }

	//新增业务方法
    public  void addedBehavior(){    
        ……
	}
}
```

#### ReadMore
[装饰模式-Decorator Pattern](https://gof.quanke.name/%E8%A3%85%E9%A5%B0%E6%A8%A1%E5%BC%8F-Decorator%20Pattern.html)

### 小结
在大致了解适配器模式与装饰模式后，我们知道适配器模式常用于协调不兼容的接口而装饰模式用于扩展功能。
接下来总结Java I/O流


## Java I/O流

### 流的概念
>数据流是一串连续不断的数据的集合，就象水管里的水流，在水管的一端一点一点地供水，而在水管的另一端看到的是一股连续不断的水流。数据写入程序可以是一段、一段地向数据流管道中写入数据，这些数据段会按先后顺序形成一个长的数据流。对数据读取程序来说，看不到数据流在写入时的分段情况，每次可以读取其中的任意长度的数据，但只能先读取前面的数据后，再读取后面的数据（不能随机读取）。不管写入时是将数据分多次写入，还是作为一个整体一次写入，读取时的效果都是完全一样的。

**概括一下，流就是有始有终、按序交付的数据序列。实际上，流对我们屏蔽了实际I/O设备处理数据的细节。**

#### 基于字节的流：输入流InputStream与输出流OutputStream
Java类库的设计者从一开始，就限定了与输入有关的类都继承自InputStream以及与输出有关的所有类都继承自OutputStream，因此可以说Java复杂的IO流其实都是InputStream和OutputStream的变体。

在JDK源码中，我们可以发现InputStream的定义为:
```
public abstract class InputStream implements Closeable
```
所以，InputStream是实现了Closeable的抽象类，需要依靠子类才能实现功能。其中，Closeable接口规定了close()方法，也就是说，所有的输入流（输出流）在使用完毕后都是需要关闭的。

按照数据输入来源的不同，继承于InputStream的子类又可以分为：
1.ByteArrayInputStream——内存缓冲区作为来源
2.FileInputStream——文件作为来源
3.StringBufferInputStream——String作为来源
4.PipedInputStream——与PipedOutputStream一起主要用于多线程环境下，实现“管道化”的概念
5.SequenceInputStream——将多个InputStream转化为单一InputStream
6.ObjectInputStream——与ObjectOutputStream一起主要用于对象的序列化

与InputStream相对应，OutputStream也是抽象类，继承于OutputStream的子类也可以根据输出目标分为：
1.ByteArrayOutputStream——内存缓冲区作为目标
2.FileOutputStream——文件作为目标
3.PipedOutputStream——与PipedInputStream一起主要用于多线程环境下，实现“管道化”的概念
4.ObjectOutputStream——与ObjectInputStream一起主要用于对象的序列化

**简而言之：InputStream与OutputStream就是以字节的方式，将数据从来源写到目标的IO工作流。**

### 装饰模式的应用
作为抽象类的InputStream与OutputStream，其实现子类不仅包括上述提及的具体构件，还包括具有装饰功能的抽象装饰类FilterInputStream与FilterOutputStream。
为了扩展多种不同的功能，如是否缓冲，是否保留所读过的行等等，JavaIO运用了装饰模式对基础InputStream(OutputStream)进行了扩展
根据所需功能的不同，FilterInputStream又可以分为：
1.DataInputStream——可以按照可移植方式从流中读取基本数据类型（int,char,long等）
2.BufferedInputStream——使用缓冲区
3.LineNumberInputStream——跟踪输入流中的行号
4.PushBackInputStream——具有“能弹出一个字节的缓冲区”，用于回退读到的最后一个字节

FilterOutputStream又可以分为：
1.DataOutputStream——可以按照可移植方式向流中写入基本数据类型（int,char,long等）
2.PrintStream——可以产生格式化输出
3.BufferedOutputStream——使用缓冲区

**通过装饰模式，在FilterInputStream(FilterOutputStream)对象的构造器中传入InputStream(OutputStream)子类对象，我们就可以为其扩展相应的功能，常用的是Buffered缓冲功能**
所以我们会经常看到：
```
BufferedInputStream bi = new BufferedInputStream(new FileInputStream(File file));
```

### 适配器模式的运用——基于字符的流：输入流Reader和输出流Writer

编码问题由来已久，基于字节的InputStream和OutputStream能够很好地处理8位的ASCII字符，但是并不能很好地处理16位的国际化字符Unicode编码，而且Java中的char类型也是16位的，也就是说，Java必须提供一种能够Unicode字符的IO流。我们已经有了基于字节的InputStream与OutputStream，为了可以得到基于字符的Reader和Writer，那么只需要用上适配器模式，添加一个适配器类就可以了。而且通过适配器模式，我们也可以很方便的为InputStream/OutputStream添加多种编码字符格式支持

在IO中，这个适配器就是InputStreamReader和OutputStreamWriter，可以将InputStream/OutputStream转化为对应的Reader/Writer根据适配器模式图，我们不难理解三者的关系。
在JDK8源码中，InputStreamReader的定义及相关构造函数：
```
public class InputStreamReader extends Reader{
	//Creates an InputStreamReader that uses the default charset.
	public InputStreamReader(InputStream in){}

	//Creates an InputStreamReader that uses the named charset.
	public InputStreamReader(InputStream in, String charsetName){}

	//Creates an InputStreamReader that uses the given charset.
	public InputStreamReader(InputStream in, Charset cs)

	//Creates an InputStreamReader that uses the given charset decoder.
	public InputStreamReader(InputStream in, CharsetDecoder dec)
}
```
因此InputStreamReader将InputStream以及特定编码字符集作为输入之后，就可以得到面向字符IO操作的Reader(Writer)了。
既然InputStream和OutputStream有众多根据来源去向不同而不同的实现类，以及运用装饰模式的相关类，那么对应的Reader和Writer也会有相应的分类,如BufferedReader、PrintWriter等等。只不过类继承关系会稍有区别。

### 小结

让我们顺着适配器模式的思路，回过头来看下FileInputStream，是不是也可以认为是一个适配器呢——将处理File的接口，转换为处理InputStream的接口。举一反三，根据适配器模式，我们就已经可以掌握绝大多数基本InputStream/OutputStream/Writer/Reader了；根据装饰模式，我们就可以掌握绝大多数具有扩展功能的InputStream/OutputStream/Writer/Reader了。剩下的问题，就是怎样从众多IO类库中选择并组合出合适IO流的问题了。
再看一遍经常碰到的代码：
```
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
```
是否能一眼看出适配器模式与装饰器模式呢？不过在这种设计下，有时候我们只需要某一个单一的流，但是却为了创建这个流而创建了多个对象，虽然灵活但同时也增加了开销。

更多参考：
[Java I/O总结](http://www.importnew.com/23708.html)

## 更多
除了IO流，[Java IO类库](http://docs.oracle.com/javase/8/docs/api/java/io/package-tree.html)还包括更多的内容
，大体上JavaIO类库（本文为java.io包）的内容还可以分为以下几块：
1.文件工具类
2.序列化与反序列化
3.压缩
4.NIO

### 文件工具类
#### File
在IO中经常要进行文件相关的IO，因此，我们就会很频繁的对文件或者文件目录进行操作，在JavaIO中就提供了File工具类方便我们进行文件目录的相关操作[File API](http://docs.oracle.com/javase/8/docs/api/index.html)

#### RandomAccessFile
有时候我们会遇到一些大文件，而我们并不需要完整的将其读出来，而是只需要读取某一部分内容，如果使用传统的InputStream和OutputStream等从头开始读到尾的IO流，明显效率低下。这个时候，我们就可以使用支持随机访问文件内容的RandomAccessFile来实现我们的需求了。不过，RandomAccessFile的绝大多数功能，已经被JDK1.4的nio的"内存映射文件(memory-mapped files)"给取代了。
更多参考[Java RandomAccessFile用法](http://blog.csdn.net/akon_vm/article/details/7429245)

### 序列化与反序列化
在JVM作用下，当对象不再被使用时，就会被回收。有时候，我们想要保存对象的状态信息，将其写到一个文本文件之类的地方存储，使得重新运行程序时，可以读取文本文件并重建该对象使得其状态与程序上次运行时相同，这时候就可以使用对象序列化技术。
在JavaIO类库中，提供了一个接口Serializable，实现了该接口的对象会被转换成一个字节序列，并且能够在以后将这个字节序列完全恢复为原来的对象。
>要序列化一个对象，首先要创建某些OutputStream对象，然后将其封装在一个ObjectOutputStream对象内。这时只需调用writeObject()即可将对象序列化，并将其发送给OUtputStream。（对象序列化是基于字节的，因此使用InputStream/OUtputStream继承层次结构）
要反序列化一个对象，需要将一个InputStream封装在ObjectInputStream内，然后调用readObject()，最后获得一个Object引用，需要进行向下转型。

更多阅读：
[Java对象的序列化与反序列化](http://www.importnew.com/17964.html)
[深入分析Java的序列化与反序列化](http://www.hollischuang.com/archives/1140)

### 压缩
JavaIO类库中也有压缩类以支持读写压缩格式的数据流，需要注意的是，这些压缩类位于java.util.zip包中而不是java.io包中；
而且由于这些类继承自FilterInputStream以及FilterOutputStream，这也就意味着这些压缩类是装饰类，在使用时直接包装InputStream/OutputStream对象即可
[JavaIO压缩流](http://blog.csdn.net/hanshileiai/article/details/6718375?reload)

### NIO
通过查看io包的源码，我们会发现一些nio的痕迹，实际上，在jdk1.4时就引入了nio以提高速度，旧的io包也使用nio重新实现了。
[Java NIO入门与详解](http://www.yangyong.me/java-nio%E5%85%A5%E9%97%A8%E4%B8%8E%E8%AF%A6%E8%A7%A3/)

## 小结
在本文中，先大致了解了两个设计模式：适配器模式，装饰模式，然后从设计模式的角度总结了JavaIO流；接着大致了解了IO类库（io包）中的文件工具类、序列化与反序列化、压缩、NIO等内容。
不得不说IO的范围非常地广，不仅涉及磁盘IO，还有网络IO等，可以从非常多的角度去总结，一篇小文章是绝对不够的而且也只能是凤毛菱角的。最后引用[深入分析JavaIO的工作机制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/#ibm-pcon)一文中的原话做个总结：

>I/O 的核心问题要么是数据格式影响 I/O 操作，要么是传输方式影响 I/O 操作，也就是将什么样的数据写到什么地方的问题，I/O 只是人与机器或者机器与机器交互的手段，除了在它们能够完成这个交互功能外，我们关注的就是如何提高它的运行效率了，而数据格式和传输方式是影响效率最关键的因素了.


最后感谢下述内容的提供，让我得以站在巨人的肩膀上：
《Java编程思想》
[进击的Java新人](https://zhuanlan.zhihu.com/hinus)
[Java I/O总结](http://www.importnew.com/23708.html)
[深入分析JavaIO的工作机制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/#ibm-pcon)