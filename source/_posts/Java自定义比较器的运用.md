---
title: Java Comparator的运用|把数组排成最小的数
date: 2017-06-21 11:00:38
categories: 技术探究
tags:
- Java
- 设计模式
---

刚好刷到一道笔试题，描述如下：
> 输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

如果排列出所有的结果，再从中选择最小的组合，这样的做法在数据量规模很大的前提下，排列的结果集为n*(n-1)*(n-2)……无疑是很不切合实际的做法。根据数字的特征，很容易就可以发现要使拼接出的数字最小，只要预先排序（数字a，b；ab>ba？b,a:a,b），再将排序后的结果集拼接起来就是答案。由于对排序算法比较熟悉，所以第一时间想到的就是快排，冒泡等常见的排序算法，但是这里的重点除了排序之外，还有排序所使用的比较规则。因此又联想到一种较为常见的排序方法，就是使用Java集合以及comparator比较器。而且由于在某一次面试中，被面试官问道如何对集合中的对象排序时还不是很熟悉自定义比较器comparator的使用，因此特意总结下关于这方面的知识点。
<!-- more -->
### 题目解答代码

```
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;

public class Solution {
    public String PrintMinNumber(int [] numbers) {
		ArrayList<Integer> list = new ArrayList<Integer>();
        if(numbers==null||numbers.length==0){
            return "";
        }
        //将数组存入List中
        int length = numbers.length;
        for(int i = 0;i<length;i++){
            list.add(numbers[i]);
        }
        //通过匿名内部类实现自定义Comparator
        //对List使用自定义规则排序
        Collections.sort(list, new Comparator<Integer>(){
        	//实现自定义Comparator
			public int compare(Integer str1,Integer str2){
   				String s1=str1+""+str2;
   				String s2=str2+""+str1;
   				//根据字典序比较
         		return s1.compareTo(s2);
     		}
  		});
        //拼接排序结果集
        StringBuffer sb = new StringBuffer();
        for(int s:list){
            sb.append(s);
        }
        return sb.toString();
    }
}
```

### Comparator自定义排序
在实现代码中，通过匿名内部类方式创建了一个比较器Comparator。对于位于java.util包中的Comparator接口，[官方文档](http://docs.oracle.com/javase/8/docs/api/index.html)中的解释是：
> 为强制对某些集合中的对象进行整体排序提供对象间相互比较功能。比较器可以传递到如Collections.sort或Arrays.sort等sort方法中对排序行为进行精确控制。比较器也可以用于控制特定数据结构（如sorted sets或sorted maps）的元素顺序或者给没有自然顺序的集合对象提供排序功能。

> 自然顺序：如数字，字符等可直接比较大小的元素顺序

也就是说，当我们想要给对象集合根据自己的意愿来排序的时候，就可以通过重写compare方法来实现比较规则，并传入sort方法中进行整体排序。
在JDK1.8之前Comparator只有两个方法
```
public interface Comparator<T> {

    public int compare(T t1, T t2);

    public boolean equals(Object object);
}
```
然而在JDK1.8中，又新增了很多方法，具体看[官方API](http://docs.oracle.com/javase/8/docs/api/index.html)，这里关注compare以及equals方法。

- compare
传入两个对象t1,t2，并在函数体内定义比较规则
若返回值>0,则说明t1>t2
若返回值==0，则说明t1==t2
若返回值<0，则说明t1<t2
*为了避免一些奇奇怪怪的情况如往TreeSet添加元素失败等，最好在实现比较规则时让compare(t1,t2)==0的布尔值与t1.equals(t2)的布尔值相等*

- equals
当且仅当object也是一个比较器并且实现同样的比较规则时返回true。
当创建一个类实现该接口时，该方法不是必须要实现的，因为每一个Oject都有equals方法

当然，如果需要比较规则可复用，我们就不能写成匿名内部类的形式了，而是需要定义一个实现Comparator接口的类，并且在实现compare方法之后将其传入sort方法中，同时我们还可以创建许多比较规则类，根据不同的情况选用不同的规则，这种编码技巧就运用到了策略模式的思想。

### 策略模式的运用

#### 介绍：算法的封装与切换
正如上述实现代码所体现的，集合中的元素与具体的排序规则分离这样一种数据与算法分离思想，就是策略模式的主要思想。

> 在策略模式中，我们可以定义一些独立的类来封装不同的算法，每一个类封装一种具体的算法，在这里，每一个封装算法的类我们都可以称之为一种策略(Strategy)，为了保证这些策略在使用时具有一致性，一般会提供一个抽象的策略类来做规则的定义，而每种算法则对应于一个具体策略类。

#### 结构图与典型代码实现
结构图：
![](http://my.csdn.net/uploads/201208/01/1343811032_3729.jpg)

代码实现：
```
abstract class AbstractStrategy {  
    public abstract void algorithm(); //声明抽象算法  
}

class ConcreteStrategyA extends AbstractStrategy {  
    //算法的具体实现  
    public void algorithm() {  
       //算法A  
    }  
}

class Context {  
private AbstractStrategy strategy; //维持一个对抽象策略类的引用  

    public void setStrategy(AbstractStrategy strategy) {  
        this.strategy= strategy;  
    }  

    //调用策略类中的算法  
    public void algorithm() {  
        strategy.algorithm();  
    }  
}
```

#### 运用
在集合排序与比较器关系中，集合就作为策略的使用类而比较器就作为策略类，实现了算法的定义与使用环境分离。关于策略模式，更多请阅读：
[策略模式-Strategy Pattern](https://gof.quanke.name/%E7%AD%96%E7%95%A5%E6%A8%A1%E5%BC%8F-Strategy%20Pattern.html)

### Comparable自然排序
在集合的排序中，除了使用自定义比较器之外，还可以通过使数据类实现java.lang.Comparable接口来实现自然排序，接口中唯一的campareTo方法称为自然比较方法。

- compareTo(Object obj)
传入一个对象，函数体内定义比较规则：
若返回值>0，则说明this>obj
若返回值==0,则说明this==obj
若返回值<0，则说明this<obj
*和Comparator一样，为了避免一些奇奇怪怪的情况，最好在实现比较规则时让this.compareTo(obj)==0的布尔值与t1.equals(t2)的布尔值相等*

因此，当类实现了Comparable接口后，就说明该类中的对象支持排序，并且可以直接使用Collections.sort（和 Arrays.sort）根据compareTo的比较逻辑进行自动排序；又或者用作sorted set或sorted map中的元素且无需指定比较器。
关于更多可直接看[官方API](http://docs.oracle.com/javase/8/docs/api/index.html)

### sort方法的排序算法
在清楚了比较规则之后，我们还需要弄清楚一个问题，就是Collections.sort方法以及Arrays.sort方法是如何根据比较规则来实现排序的。
查看Collections的源码，
```
	@SuppressWarnings({"unchecked", "rawtypes"})
    public static <T> void sort(List<T> list, Comparator<? super T> c) {
        list.sort(c);
    }

```
发现其中的sort方法只是调用List的sort方法，那也就是说，真正的排序逻辑在List的实现类中，以ArrayList为例，其源码中sort部分：
```
	@Override
    @SuppressWarnings("unchecked")
    public void sort(Comparator<? super E> c) {
        final int expectedModCount = modCount;
        Arrays.sort((E[]) elementData, 0, size, c);
        if (modCount != expectedModCount) {
            throw new ConcurrentModificationException();
        }
        modCount++;
    }
```
*其中modCount主要在多线程环境下需要使用，防止一个线程正在迭代遍历，另一个线程修改了这个列表的结构。[更多](http://blog.csdn.net/upcye/article/details/48752427)。*
可见，List的sort方法也是调用了Arrays.sort，接下来看相关源码：
```
	public static <T> void sort(T[] a, int fromIndex, int toIndex,Comparator<? super T> c) {
        if (c == null) {
            sort(a, fromIndex, toIndex);
        } else {
            rangeCheck(a.length, fromIndex, toIndex);
            if (LegacyMergeSort.userRequested)
                legacyMergeSort(a, fromIndex, toIndex, c);
            else
                TimSort.sort(a, fromIndex, toIndex, c, null, 0, 0);
        }
    }
```
因此可以得出结论，在JDK8中运用比较器的Arrays.sort方法主要运用了两种排序算法,归并排序以及结合归并排序和插入排序的[TimSort](http://blog.csdn.net/yangzhongblog/article/details/8184707)。相关排序算法原理就暂不说明了，建议了解大体算法思想后直接读源码。

### 小结
本文先从一道简单的笔试题出发，了解Comparator在排序中的相关使用，进而从中熟悉策略模式，并了解另外一种比较技巧——实现Comparable，最后了解JDK中的排序算法。
关于Comparator与Comparable的区别，我的理解是：Comparator只是一个比较器，对象集合需要引入该比较器才可以相互比较，属于外部范畴；而Comparable是一种功能，实现该接口的对象集合中的对象都具有可比较的特性，属于内部范畴。举个栗子，人本来不能飞，但是可以通过坐飞机或者坐飞船来实现飞行的梦想（Comparator）；而有些人基因变异，长出了翅膀，因此并不需要辅助工具，也能够飞（Comparable）。


