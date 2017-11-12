---
title: 经典内部排序算法学习总结——算法思想、可视化、Java代码实现、改进、复杂度分析、稳定性分析
date: 2017-07-17 22:25:37
categories: 技术探究
tags:
- 算法
---

此文属于旧文重发，[原文章链接](http://blog.csdn.net/github_37022917/article/details/57088028)
<!-- more -->
### 什么是排序算法？
排序，顾名思义，就是按照一定的规则排列事物，使之彼此间有序
而排序算法所要做的工作，就是将数据按照人为制定的比较规则排列好，使数据处于彼此间有序的状态。


### 为什么要进行排序？
那为什么要将数据排序呢？计算机处理速度这么快，会不会有点多此一举。现在考虑手上有一本目录乱序的词典，假设有1w个单词，如果想要查apple这个单词，每次都要从头开始找，一个个的确定是不是apple，忽略心力交瘁和砸字典的冲动，那么假设每次查找都需要12个小时。好，现在手上有一本有序的牛津词典，就是当今经常看到的这种，每次我们查apple这个词时，就可以根据字母的顺序，不到一分钟就可以找出apple的释义了。这样一看，有序与无序就相差了12个小时，而且是每次查找都节省12个小时，则让每个人都可以节省更多的时间去做其他的事情。类比到计算机也是一样的道理，节省下来的时间资源是巨大的，这种规模效益无疑值得我们去排序。

>有时候，告诉一个人**怎样**去做一件事不如告诉他**为什么**要这么做。例如，告诉一个新手程序员怎样去优化一段代码，他有可能会拖延，但是告诉他说，一旦完成优化，每个用户浏览所花费的时间都会节省5秒。这样出来的效果是不一样的。——观点来源于网络

因此，觉得网络上很多文章一上来就直接说排序算法的思想以及如何实现是不够的。先要弄清楚为什么要排序，再去了解排序算法的细枝末节，毕竟所有排序算法，都是为了一个目的服务的——节约时间。


### 三、经典内部排序算法思想、可视化、Java代码实现、改进方法、时间复杂度、空间复杂度、稳定性
结论先行，接下来会有大量篇幅去讲述排序算法的细节。为了方便，数组元素都使用整数。
![结论](http://images.cnitblog.com/blog/512692/201304/12095757-775cf861406644bfad60ff2763f499e4.png)
博客参考：
[八大排序算法](http://blog.csdn.net/hguisu/article/details/7776068)
[十种排序算法总结](http://blog.csdn.net/jnu_simba/article/details/9705111)
[视觉直观感受7种常用的排序算法](http://blog.jobbole.com/11745/)
[常用排序算法稳定性、时间复杂度分析(转，有改动)](http://www.cnblogs.com/nannanITeye/archive/2013/04/11/3013737.html)
[常用排序算法时间复杂度分析](http://whatsdjgpp.github.io/2014/04/12/%E5%A4%8D%E6%9D%82%E5%BA%A6%E5%88%86%E6%9E%90/)
[十大经典排序算法|JavaScript描述](http://damonare.github.io/2016/09/16/%E5%8D%81%E5%A4%A7%E7%BB%8F%E5%85%B8%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%E6%80%BB%E7%BB%93%EF%BC%88javascript%E6%8F%8F%E8%BF%B0%EF%BC%89/)

算法可视化网站：
[VisuAlgo](https://visualgo.net/sorting)
书籍：Data Structures and Algorithm Analysis in Java (Thrid Edition)


#### 冒泡排序BubbleSort
##### 介绍：
冒泡排序是一种较为容易理解的排序算法，因为它就是相邻数两两比较，符合条件就交换位置而已，如果从小到大排的话，就像水中升起的泡泡一样越来越大

##### 算法步骤：基于交换
假设数组a[n]有N个整数
第一趟，第一个数与第二个数比较，符合条件就交换位置，然后第二个数和第三个数比较，符合条件就交换位置，以此类推。如此，最后一个数字为最大数；
第二趟，除去第一趟最后一个数字，第一个数与第二个数比较，符合条件就交换位置，如此类推，此时最后一个数字为本趟最大数
第三趟，如上类推
……
第N-1趟，如上类推，所有交换完成后数组元素有序

##### 可视化：
![冒泡排序](http://upload.wikimedia.org/wikipedia/commons/c/c8/Bubble-sort-example-300px.gif)

##### Java代码实现：
```
public class BubbleSort {
    public static void sort(int[] a){
        int temp;//定义用于交换的临时变量
        int length = a.length;//定义递减长度变量
        
        //外循环，冒泡排序进行的趟数，取a.length-1是因为最后一趟只有一个数字，没有必要排序
        for(int i=0;i<a.length-1;i++){
            //内循环，实际进行两两比较
            for(int j=0;j<length-1;j++){
                if(a[j]>a[j+1]){
                    temp = a[j+1];
                    a[j+1] = a[j];
                    a[j] = temp;
                }
            }
            length--;//每趟结束后，最后一个数字有序且为该趟最大，下一趟排序不必进行比较
            
            //用于在控制台输出每一趟的结果
            System.out.println();
            System.out.print("第"+(i+1)+"趟排序");
            for(int k = 0;k<a.length;k++){
                System.out.print(a[k]+" ");
            }
        }
    }
    
    public static void main(String[] args) {
        int[] a = {99,89,76,66,54,47,32,20,18,5};
        System.out.print("原数组：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
        sort(a);
        System.out.println();
        System.out.print("冒泡排序结束后：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
    }
}
```
运行结果：
![](http://img.blog.csdn.net/20170225180742509?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZ2l0aHViXzM3MDIyOTE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
##### 改进方法：
（1）设置标志位，每一趟循环开始默认有序，当发生交换时，则数组无序，仍需继续循环
参考博客[白话经典算法系列之一  冒泡排序的三种实现](http://blog.csdn.net/morewindows/article/details/6657829)
```
public static void improveBubbleSort(int[] a){
        int temp;//定义用于交换的临时变量
        int length = a.length;//length存储数组长度，可以避免每次循环中调用a.length方法的消耗
        boolean flag = true;//设置标志位为ture，假设初始状态无序
        
        //若无序，则进行循环
        while(flag){
            flag = false;//假设元素正序
            for(int i = 0;i<length-1;i++){
                if(a[i]>a[i+1]){
                    temp = a[i+1];
                    a[i+1] = a[i];
                    a[i] = temp;
                    //若发生交换操作，则为无序状态，仍需进行下一次循环
                    //若不发生交换操作，则此步不会执行，则为正序，只需执行一趟循环
                    flag=true;
                }
            }
        }
    }
```
（2）轮流从左到右以及从右到左进行冒泡排序
参考博客：[排序算法系列：冒泡排序与与双向冒泡排序](http://blog.csdn.net/lemon_tree12138/article/details/50591859)

（3）同样是设置标志位，不过这个标志位用于记录最后一次发生交换的位置，则下一次循环只需要执行到该位置即可，因为后面的元素已经有序。

##### 时间复杂度：
与比较次数、逆序数有关，一次交换减少一个逆序
最好情况为O($N$)。按照最开始的思路，假设元素一开始全部有序，但即使不需要交换，都需要不断循环比较，N个元素两两比较共需要$\frac{N(N-1)}{2}$次，再加上其他赋值等操作，所以时间复杂度为O($N^2$)；改进方法一，若元素一开始全部有序，则一次循环即可比较次数为N-1，再加上其他赋值等操作，所以时间复杂度为O($N$)。

最坏情况为O($N^2$)。元素一开始全部逆序，则逆序数有$\frac{N(N-1)}{2}$个，按照最开始的思路，则需要经历N-1趟，共$\frac{N(N-1)}{2}$次比较与交换，再加上其他赋值等操作，所以时间复杂度为O($N^2$)；采用改进一的算法，也需要进行N-1次循环，所以时间复杂度为O($N^2$)。

平均情况为O($N^2$)。书上定理：N个互异数的数组的平均逆序数是$\frac{N(N-1)}{4}$个；通过交换相邻元素进行排序的任何算法平均都需要O($N^2$)时间。为了消除对应的逆序数，所以时间复杂度为O($N^2$)。

##### 空间复杂度：
由于需要使用一个用于交换的临时变量temp，与数组规模N无关，所以空间复杂度为O(1)

##### 稳定性分析：
由于两相等元素在冒泡排序前后的相对位置不变（相等不发生交换），所以是稳定的


#### 选择排序SelectionSort
##### 介绍：
和冒泡排序一样通俗易懂，顾名思义，选择排序就是在待排序数组中选择出最小（最大）的元素，放到最前面；然后再在待排序数组中选出最小（最大）的元素放到前面，以此类推。

##### 算法步骤：基于选择
假设数组a[n]有N个整数，
第一趟，从数组中找出数组中最小的元素，将其与第一位的元素交换位置
第二趟，除去数组中的第一个元素，从剩下的元素中，找出最小的元素，将其与剩下的元素中的第一位交换位置
……
以此类推，
第N-1趟，完成上述选择排序，数组有序
*也可以找出最大元素放在数组最后面，以此类推*

##### 可视化：
![选择排序](http://img.blog.csdn.net/20160621101113555)

##### Java代码实现：
参考博客：[白话经典算法系列之四 直接选择排序及交换两个数据的正确实现](http://blog.csdn.net/morewindows/article/details/6671824)
```
public class SelectionSort {
    public static void sort(int[] a){
        int temp;//定义用于交换的临时变量
        int length = a.length;//存储渐变数组长度
        int maxPos = 0;//定义最大元素位置
        
        for(int i = 0;i<a.length-1;i++){
            //找出最大元素的位置
            for(int j = 1;j<length;j++){
                if(a[maxPos]<a[j]){
                    maxPos = j;
                }
            }
            //交换数组元素的位置
            temp = a[length-1];
            a[length-1] = a[maxPos];
            a[maxPos] = temp;
            
            length--;//除去最后那位最大的元素
            maxPos = 0;//最大元素位置置0
            
            //用于在控制台输出每一趟的结果
            System.out.println();
            System.out.print("第"+(i+1)+"趟排序");
            for(int k = 0;k<a.length;k++){
                System.out.print(a[k]+" ");
            }
        }
        
    }
    
    public static void main(String[] args) {
        int[] a = {99,74,25,88,54,63,41,33,4,17};
        System.out.print("原数组：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
        sort(a);
        System.out.println();
        System.out.print("选择排序结束后：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
    }
}
```
运行结果：
![](http://img.blog.csdn.net/20170225181641314?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZ2l0aHViXzM3MDIyOTE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

##### 改进方法：
（1）同时找最大的元素位置和最小元素的位置
参考博客：[排序算法（二）——选择排序及改进](http://blog.csdn.net/u012152619/article/details/47306053)
```
public static void improveSort(int[] a){
        int maxTemp;//定义用于交换的临时变量
        int minTemp;//定义用于交换的临时变量
        int length = a.length;//存储渐变数组长度
        int maxPos;//定义最大元素位置
        int minPos;//定义最小元素位置
        
        //由于一趟确定两个数，所以趟数为原来的1/2
        for(int i = 0;i<a.length/2;i++){
            maxPos = i;
            minPos = i;
            //同时找出最大元素位置及最小元素位置
            for(int j = i+1;j<length;j++){
                if(a[maxPos]<a[j]){
                    maxPos = j;
                }
                if(a[minPos]>a[j]){
                    minPos = j;
                }
            }
            //存储最大最小元素数值,最后用于覆盖首尾位置
            maxTemp = a[maxPos];
            minTemp = a[minPos];
            //如果第一个元素不是最大最小值，就是需要保护的数值,需要找一个不是首尾位置的地方存储，优先考虑maxPos和minPos
            if(a[i]!=maxTemp&&a[i]!=minTemp){
                if(maxPos!=i&&maxPos!=length-1){//如果最大元素位置不是首尾，则可以覆盖
                    a[maxPos]=a[i];
                }
                if(minPos!=i&&minPos!=length-1){//如果最小元素位置不是首尾，则可以覆盖
                    a[minPos]=a[i];
                }
            }
            //如果最后一个元素不是最大最小值，就是需要保护的数值，需要找一个不是首尾位置的地方存储，优先考虑maxPos和minPos
            if(a[length-1]!=maxTemp&&a[length-1]!=minTemp){
                if(maxPos!=i&&maxPos!=length-1){//如果最大元素位置不是首尾，则可以覆盖
                    a[maxPos]=a[length-1];
                }
                if(minPos!=i&&minPos!=length-1){//如果最小元素位置不是首尾，则可以覆盖
                    a[minPos]=a[length-1];
                }
            }
            //最后覆盖首尾位置
            a[i] = minTemp;
            a[length-1] = maxTemp;
            
            length--;//除去最后那位最大的元素
        }
    }
```
*注意，在这种同一个循环中，既要交换最大位置元素，又要交换最小位置元素的情况。如果先后交换，如
swap(a[maxPos],a[length-1])
swap(a[minPos],a[i])
则最先的那个交换有可能会改变后一次交换最小位置原本的数值，如minPos==length-1*

##### 时间复杂度：
最好情况、最坏情况、平均情况均为O($N^2$)
采用改进前的算法，无论数组元素全部有序还是全部无序，都需要执行N-1趟共$\frac{N(N-1)}{2}$次比较来确定最大（小）值，再加上其他赋值等操作，平均也需要时间复杂度O($N^2$)。即时采用改进后的算法，趟数减少一半，但是一趟的比较次数增加一倍，时间复杂度还是O($N^2$)。

##### 空间复杂度：
需要的额外辅助空间（用于交换等）与数据规模大小无关，所以为O(1)
##### 稳定性分析：
由于先扫描先排序，从左到右进行的话，相等的元素a左有可能被置于右边，相等的元素a右有可能被置于左边，所以是不稳定的


#### 插入排序InsertiontSort
##### 介绍：
插入排序也是比较容易的排序算法之一，主要是将待排序的数组的每一个元素插入到有序数组的对应位置上
##### 算法步骤：基于插入
假定数组a[n]有N个元素，同时假设第一个元素a[0]位于有序区，剩余元素皆位于无序区
第一趟，将无序区的第一个元素a[1]，插入到有序区中，若比a[0]小，则将a[0]的数右移到a[1]处，将a[1]的元素插入到a[0]的位置上，有序区为a[0]、a[1]
第二趟，将无序区的第一个元素a[2]，插入到有序区对应的位置上，相应的元素移动使有序区维持有序，
……
第N-1趟，完成插入操作，数组有序

##### 可视化：
![插入排序](http://upload.wikimedia.org/wikipedia/commons/0/0f/Insertion-sort-example-300px.gif)
##### Java代码实现：
```
public class InsertionSort {
    public static void sort(int[] a){
        int temp;//定义临时变量用于存储待插入元素
        int length = a.length; //存储数组a的长度
        int pos;//定义要插入的位置
        
        //开始遍历，无序区从a[1]开始
        for(int i = 1;i<length;i++){
            temp = a[i];
            for(pos = i;pos>0&&temp<a[pos-1];pos--){
                a[pos] = a[pos-1];
            }
            a[pos] = temp;
            
            //用于在控制台输出每一趟的结果
            System.out.println();
            System.out.print("第"+i+"趟排序");
            for(int k = 0;k<a.length;k++){
                System.out.print(a[k]+" ");
            }
        }
        
    } 
    
    public static void main(String[] args) {
        int[] a = {99,74,25,88,54,63,41,33,4,17};
        System.out.print("原数组：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
        sort(a);
        System.out.println();
        System.out.print("插入排序结束后：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
    }
}
```
运行结果
![](http://img.blog.csdn.net/20170225181722852?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZ2l0aHViXzM3MDIyOTE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
##### 改进方法：
（1）直接插入排序通过顺序比较来确定插入点，比较次数较多，而这可以通过二分查找的方法在有序区中确定插入点来减少比较次数，快速定位插入点。
参考博客：排序算法（三）——[插入排序及改进](http://blog.csdn.net/u012152619/article/details/47306209)

##### 时间复杂度：
最好情况为O($N$)。如果数组一开始全部有序，则只需要进行一遍外循环即可，内循环因条件不成立无法开始，此时时间复杂度O($N$)
最坏情况为O($N^2$)。如果数组一开始反序，则需要经过N-1趟共$\frac{N(N-1)}{2}$次比较和移动，再加上其他操作，则时间复杂度为O($N^2$)
平均情况为O($N^2$)。从某种意义上看，插入排序隐含地通过交换相邻元素完成排序（待插入元素与被移动元素逐一交换），因此也可看做与逆序数相关，平均逆序数$\frac{N(N-1)}{4}$个，因此时间复杂度为O($N^2$)。

##### 空间复杂度：
由于所需额外辅助空间与数据规模N无关（只需要一个临时存储待插入元素空间），因此空间复杂度为O(1)
##### 稳定性分析：
因为相等的元素，位于左边的先进入有序区，右边的进入有序区后也不会插入等左的左边，因此是稳定的

#### 希尔排序ShellSort
##### 介绍：
插入排序的升级版，使用不同的增量依次把原数组分割成不同的子序列，对每个子序列进行插入排序，随着算法的进行，增量逐渐减少，直到比较相邻元素的最后一趟排序为止，因此希尔排序也叫做缩减增量排序。因为插入排序对有序的情况效率较高，而随着算法的进行，数组元素趋于有序，所以希尔排序的效率也比较高

##### 算法步骤：基于插入
假设数组a[n]有N个元素，选择增量为{1,3,5}
第一趟排序，根据增量为5，将原数组分割为5个子序列，如{a[0]、a[5]、a[15]、……}、{a[1]、a[6]、a[11]、……}、{a[2]、a[7]、……}、……，依次对处于这些位置的序列进行插入排序
第二趟排序，根据增量为3，将第一趟排序结果分割为3个子序列，如{a[0]、a[3]、a[6]、……}、{a[1]、a[4]、a[7]、……}、……依次对处于这些位置的序列进行插入排序
第三趟排序，因为增量为1，对上一趟排序结果进行插入排序，算法结束

##### 可视化：
![希尔排序](http://img.blog.csdn.net/20160621105744886)

##### Java代码实现：
```
public class ShellSort {
    //以N/2、N/4、……为增量序列
    public static void sort(int[] a){
        int pos;//定义要插入的位置
        int temp;////定义临时变量用于存储待插入元素
        int length = a.length;
        
        for(int gap = length/2;gap>0;gap/=2){//根据增量确定趟数
            for(int i = gap;i<length;i++){//执行插入排序，从0到gap-1为每子序列的有序区
                temp = a[i];
                for(pos = i;pos>=gap&&temp<a[pos-gap];pos-=gap){
                    a[pos] = a[pos-gap];
                }
                a[pos] = temp;
            }
            //用于在控制台输出每一趟的结果
            System.out.println();
            System.out.print(gap+"排序后：");
            for(int i = 0;i<a.length;i++){
                System.out.print(a[i]+" ");
            }
        }
    }
    
    public static void main(String[] args) {
        int[] a = {99,74,25,88,54,63,41,33,4,17};
        System.out.print("原数组：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
        sort(a);
        System.out.println();
        System.out.print("插入排序结束后：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
    }
}
```
运行结果
![](http://img.blog.csdn.net/20170225182617645?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZ2l0aHViXzM3MDIyOTE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
##### 改进方法：
（1）希尔排序的运行时间依赖于增量序列的选择，通过改变增量序列，来改进算法的运行时间。

##### 时间复杂度：
目前只有最坏情况时间复杂度，其他情况尚无结论，由于过于复杂，所以一般很少用希尔排序
使用希尔增量时，最坏情况时间复杂度为O($N^2$)
使用Hibbard增量时，最坏情况时间复杂度为O($N^\frac{3}{2}$)
##### 空间复杂度：
与插入排序类似，额外辅助空间与数组元素规模N无关，所以空间复杂度为O(1)
##### 稳定性分析：
由于不同趟之前会彼此打乱相等元素的相对位置，所以希尔排序是不稳定的

#### 堆排序HeapSort
参考博客：[常见排序算法-堆排序](http://bubkoo.com/2014/01/14/sort-algorithm/heap-sort/)
##### 介绍：
和选择排序类似，堆排序也是从待排序数组中选择出最大（小）值，然后进行多趟选择完成排序，但是堆排序相对直接选择排序一个很大的区别在于，堆排序利用了堆这种数据结构——堆可以看成完全二叉树，所以每个节点可以对应数组元素，其堆序性质，父节点比子节点大（父节点比子节点小）进而使堆为最大（小）堆，根节点是最大（小）的。因此，堆排序实际上就是不断地取出根节点，然后调整堆结构维持堆序性质，再取出根节点……最后取出的元素序列有序。

##### 算法步骤：基于选择
假设数a[n]有N个元素，要求排序后的数组从小到大，
第一趟，以线性时间建立一个最大堆，以层序遍历的方式对应数组的位置（下标从0开始），则a[0]为根节点，交换此时a[0]与a[n-1]的位置，此时被放到最后的元素已经不再属于堆，然后对改变后的堆执行下滤（将a[0]放到合适的位置），成立一个新堆。
第二趟，再次交换数组首尾位置的元素，此时被放到最后的元素已经不再属于堆，接着对改变后的堆执行下滤操作（将a[0]放到一个合适的位置），重新建立堆。
……
以此类推，第N-1趟后，数组有序

##### 可视化：
![堆排序](http://bubkoo.qiniudn.com/Sorting_heapsort_anim.gif)
##### Java代码实现：
```
public class HeapSort {
    //找出节点的左儿子,数组下标从0开始，则节点pos的左儿子为2pos+1，右儿子为2pos+2
    private static int leftChild(int pos){
        return 2*pos+1;
    }
    //执行下滤操作，维持堆序性质
    private static void percDowm(int[] a,int pos,int length){
        int child;//子节点的下标
        int temp;//定义临时变量用于存储被下滤的节点元素
        
        //下滤操作，按照父节点比子节点大的堆序性质，找出某一节点合适的position
        for(temp=a[pos];leftChild(pos)<length;pos=child){
            child = leftChild(pos);
            if(child!=length-1&&a[child]<a[child+1]){//如果左儿子不是尾节点，则肯定有右儿子。找出两个儿子中最大者
                child++;
            }
            if(temp<a[child]){//子节点若比父节点大，则子节点元素移到父节点的位置
                a[pos]=a[child];
            }
            else{
                break;
            }
        }
        a[pos] = temp;
    }
    
    public static void sort(int[] a){
        int length = a.length;
        int temp;//定义临时空间用于交换
        
        //建立一个堆，不考虑树叶（树叶没有子节点）
        for(int i=length/2-1;i>=0;i--){
            percDowm(a, i,length);
        }
        //提取最大元素，并调整堆结构
        for(int i=0;i<a.length-1;i++){
            temp = a[0];
            a[0] = a[length-1];
            a[length-1] = temp;
            
            length--;//除去最后一位元素
            percDowm(a,0,length);
            
            //用于在控制台输出每一趟的结果
            System.out.println();
            System.out.print("第"+(i+1)+"趟排序");
            for(int k = 0;k<a.length;k++){
                System.out.print(a[k]+" ");
            }
        }
    }
    
    public static void main(String[] args) {
        int[] a = {99,74,25,88,54,63,41,33,4,17};
        System.out.print("原数组：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
        sort(a);
        System.out.println();
        System.out.print("堆排序结束后：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
    }
}
```
运行结果：
![](http://img.blog.csdn.net/20170225181812096?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZ2l0aHViXzM3MDIyOTE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
##### 时间复杂度：
与建立堆时以及下滤时的比较次数有关，平均情况，最好情况，最坏情况。均为$O(N\log N)$.
>书中定理，对N个互异项的随机排列进行堆排序所用比较的平均次数为$2N\log N-O(N\log N)$

因此平均情况时间复杂度为$O(N\log N)$，而且，无论什么情形，基本都要进行这么多次比较以及其他操作，所以平均情况，最好情况，最坏情况的时间复杂度均为$O(N\log N)$.
##### 空间复杂度：
由于额外辅助空间与数据规模大小N无关，所以为O(1)
##### 稳定性分析：
由于连续的下滤操作会打破堆的结构，因此是不稳定的


#### 归并排序MergeSort
##### 介绍：
归并排序运用经典的分而治之策略，其将问题分成一些小的问题进行递归求解，而治的阶段则将分的阶段解得的各答案修补在一起。
##### 算法步骤：基于递归
假设数组a[n]有N个元素，首先申请一个具有同样空间大小的数组，用于存放最后的有序数组
要对这N个元素进行排序，则可以通过将其分成前后两半，分别排序后进行合并
同理，要对前后两半序列排序，则可以先把前半部分再分成两半，分别排序后合并，对于后半部分也是如此
如此多次递归之后，排序变成对基准情况——只有一个元素的序列（因为一个元素默认其有序）进行合并，得出的结果再返回进行合并，多次合并后，最后数组元素有序

如何合并两个有序序列呢？两个序列从起始位开始比较，较小（大）的放到临时数组中，然后相关的指针指向下一个元素，再次进行比较，当其中一个序列完成所有比较后，则将另外一个序列剩余部分放到临时数组中

##### 可视化：
![归并排序](http://img.blog.csdn.net/20160621141134391)

##### Java代码实现：
```
public class MergeSort {
    /*
     * 归并排序内部实现
     * a 为待排序数组
     * tempArray 为临时数组，用于存储合并之后的结果
     * left 要执行归并排序的序列头
     * right 要执行归并排序的序列尾
     */
    private static void mergeSort(int[] a ,int[] tempArray,int left,int right){
        
        if(left<right){
            int center = (left +right)/2;
            mergeSort(a, tempArray, left, center);//对左半部分递归执行归并排序
            mergeSort(a, tempArray, center+1, right);//对右半部分递归执行归并排序
            merge(a,tempArray,left,center+1,right);//合并左右两半部分
        }
    }
    
    /*
     * 合并两个有序序列
     * a 为待排序数组
     * tempArray 为临时数组，用于存储合并之后的结果
     * leftPos 数组左半部分的比较位置
     * rightPos 数组右半部分的比较位置
     * rightEnd 数组右半部分的末尾
     */
    private static int[] merge(int[] a,int[] tempArray,int leftPos,int rightPos,int rightEnd){
        int leftEnd = rightPos-1;//数组左半部分的末尾
        int tempPos = leftPos;//在临时数组中存放元素的位置
        int num = rightEnd-leftPos+1;//需要合并的元素数量
        
        //左右两半部分有序序列，从初始位置开始比较，较小的放进临时数组中，之后相应的位置指针右移
        while(leftPos<=leftEnd&&rightPos<=rightEnd){
            if(a[leftPos] <= a[rightPos]){
                tempArray[tempPos++]=a[leftPos++];
            }else{
                tempArray[tempPos++]=a[rightPos++];
            }
        }
        //若左边序列还有剩余未比较元素，则全部复制到临时数组中
        while(leftPos<=leftEnd){
            tempArray[tempPos++] = a[leftPos++]; 
        }
        //若右边序列还有剩余未比较元素，则全部复制到临时数组中
        while(rightPos<=rightEnd){
            tempArray[tempPos++] = a[rightPos++];
        }
        //最后把完成合并的临时数组的元素复制回原数组a中
        for(int i = 0;i<num;i++,rightEnd--){
            a[rightEnd] = tempArray[rightEnd];//rightEnd为完成合并的序列末尾元素在数组中的位置
        }
        //用于在控制台输出每一趟的结果
        System.out.println();
        System.out.print("归并排序");
        for(int k = 0;k<a.length;k++){
            System.out.print(a[k]+" ");
        }
        return a;
    }
    //外部可调用的归并排序
    public static void sort(int[] a){
        int[] tempArray = new int[a.length];//创建临时数组
        mergeSort(a, tempArray, 0, a.length-1);//开始归并排序
    }
    
    
    public static void main(String[] args) {
        int[] a = {99,74,25,88,54,63,41,33,4,17};
        System.out.print("原数组：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
        sort(a);
        System.out.println();
        System.out.print("归并排序最终结束后：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
    }
}
```
运行结果：
![](http://img.blog.csdn.net/20170225181951496?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZ2l0aHViXzM3MDIyOTE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

##### 时间复杂度：
最好、最坏、平均情况均为$O(N\log N)$
关于递归算法的时间复杂度分析可以了解主项定理的相关知识[主项定理——算法复习笔记](http://ych8700.blog.163.com/blog/static/149526022006112565136/)
对递归程序进行分析必须要有运行时间的递归关系。在这里，
当N=1时，归并排序运行时间是常数，记为1；
当对N个数进行排序时，其用时等于完成两个大小为N/2的递归排序再加上线性的合并时间，因此可得递归关系：
$T(1)=1$
$T(N)=2T(N/2)+N$
最后解出$T(N)=N\log N+N$，所以时间复杂度为$O(N\log N)$，而这无论是最好最坏情形还是平均情形都是基本不变的
>归并排序的运行时间严重依赖于比较元素和在数组中（以及临时数组）中移动元素的相对开销，而这些开销是和语言相关的。
在Java中，执行一次泛型排序（使用Comparator）时，进行一次元素比较可能是昂贵的（因为比较可能不容易被内嵌，从而动态调度的开销可能会减慢执行的速度），但是移动元素则是省时的（因为他们是引用的赋值，而不是庞大对象的拷贝）。**归并排序是流行算法中比较次数最少的，因此适用于使用Java的通用排序算法，而它就是标准Java类库中泛型排序所使用的的算法**
在C++的泛型排序中，如果对象庞大，那么拷贝对象可能需要很大的开销，而由于编译器具有主动执行内嵌优化的能力，因此比较对象常常是省时的，因此，C++最好使用比较次数多而移动数据少的算法，其标准库中常使用的是快速排序

##### 空间复杂度：
由于需要一个额外的数组来存储归并之后的结果，其大小与N的大小有关，因此空间复杂度为O(N)
##### 稳定性分析：
由于递归合并的操作并没有打乱两相等元素的相对位置，因此是稳定的

#### 快速排序QuickSort
参考博客：[白话经典算法系列之六 快速排序](http://blog.csdn.net/morewindows/article/details/6684558)
##### 介绍：
快速排序是实践中一种快速的排序方法，在C++中或对Java基本类型的排序中特别有用。和归并排序一样，快速排序也是一种分治的递归算法。

##### 算法步骤：
假设数组a[n]有N个元素
首先从数组中选出一个枢纽元，然后把元素分成两个部分，一部分比枢纽元大，另一部分比枢纽元小，枢纽元位于两部分之间，则枢纽元处于排序后正确的位置上；
从两部分元素集合中各自选择枢纽元，如上述所示将其所在集合分成两个部分
不断递归子集合，直到基准情况——子集合中元素个数是0或1时,，最后数组有序

##### 可视化：
![快速排序](http://img.blog.csdn.net/20160621100914382)
##### Java代码实现：
```
public class Quick {
    public static void quickSort(int[] a,int left,int right){
        if(left<right){
            int pivot = a[left];//以第一个数作为枢纽元（挖坑）
            int i = left,j=right;
            while(i<j){
                while(i<j&&a[j]>=pivot){//从右边起找第一个小于枢纽元的数
                    j--;
                }
                if(i<j){
                    a[i++]=a[j];//填上坑，并再挖坑
                }
                while(i<j&&a[i]<=pivot){//从左边起找第一个大于枢纽元的数
                    i++;
                }
                if(i<j){
                    a[j--] = a[i];//填上坑，并再挖坑
                }
            }
            a[i] = pivot;//最后用枢纽元填坑
            //递归两部分数组
            quickSort(a, left, i-1);
            quickSort(a, i+1, right);
        } 
    }
    public static void main(String[] args) {
        int[] a = {99,74,25,88,54,63,41,33,4,17,45,66,89,3,25,4,7};
        System.out.print("原数组：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
        quickSort(a, 0, a.length-1);
        System.out.println();
        System.out.print("快速排序最终结束后：");
        for(int i = 0;i<a.length;i++){
            System.out.print(a[i]+" ");
        }
    }
}
```
运行结果：
![](http://img.blog.csdn.net/20170225182419302?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZ2l0aHViXzM3MDIyOTE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 

##### 改进方法：
（1）
参考书籍：《数据结构与算法Java语言描述》例程
参考博客：[三种快速排序的算法以及快速排序的优化](http://www.codeceo.com/article/3-sort-quick-sort-improve.html)
>**关于枢纽元的选择——最好选择数组的中值**
常见的做法是选择第一个元素作为枢纽元，但是在这种选择下，如果元素已经有序（正序或反序），则分割成两半的时候就会将全部元素分割到其中一个集合中，而且对以后的递归过程也是如此。
较好的做法是采用三数中值分割法——使用左端、右端和中心位置上的三个元素的中值作为枢纽元，这样可以消除预排序的坏情形
**关于分割策略——如何将小元素移到数组的左边，把大元素移到数组的右边。**策略有很多种，这里使用一种已知是安全的
首先，将枢纽元与最后的元素交换使枢纽元离开被分割的数据段。
定义两个变量i，j，其中 i 从第一个元素开始，而 j 从倒数第二个元素开始
当 i 在 j 的左边时，将 i 右移，移过那些小于枢纽元的元素，并将 j 左移，移过那些大于枢纽元的元素。则当 i 和 j 停止时， i 指向一个大元素而 j 指向一个小元素，这时如果 i 还在 j 的左边，则交换两个元素的位置。然后重复上述过程，知道 i 和 j 彼此交错
最后，交换枢纽元与 i 所指向的元素（因为小于位置 i 的必定是小元素，大于位置 i 的必定是大元素）
**对于与枢纽元相等的元素，则让 i 和 j 停止**
**关于小数组（N<=20时）,插入排序比快速排序更有效率。**所以通常对于小数组更倾向于使用插入排序这种排序算法，相对于自始至终使用快速排序可以节省15%的运行时间。**分界通常选取N=10**
```
public class QuickSort {
    private static void swap(int[] a,int left,int right){
        int temp;
        temp = a[left];
        a[left] = a[right];
        a[right] = temp;
    }
    /*
     * 三数中值分割法选择枢纽元
     * 
     * 对a[left]、a[right]、a[center]适当的排序后，取中值作为枢纽元。
     * 此时三元素的最小者位于a[left]，是恰当的位置，最大者位于a[right]也是恰当的位置，
     * 
     * 而把枢纽元放到a[right-1]上并在分割阶段将i和j初始化为left+1和right-2。
     * 此时a[left]可以充当j的哨兵
     * 而且由于i将停在那些等于枢纽元的元素上，所以枢纽元放在a[right-1]上也可以充当i的哨兵
     */
    private static int medianPivot(int[] a,int left,int right){
        int center = (left+right)/2;
        //三数排序，中值为枢纽元
        if(a[center]<a[left]){
            swap(a,left,center);
        }
        if(a[right]<a[left]){
            swap(a,left,right);
        }
        if(a[right]<a[center]){
            swap(a,center,right);
        }
        //把枢纽元放在right-1的位置上
        swap(a,center,right-1);
        return a[right-1];
    }
    //快速排序内部例程
    private static void quickSort(int[] a,int left,int right){
        //数组规模N>10使用快排,N<=10使用插入排序
        if(left + 10 <= right){
            int pivot = medianPivot(a, left, right);//三数中值分割法已经把left与right-1位置上的元素分成两部分
            int i =left,j=right-1;
            for(;;){
                while(a[++i]<pivot){}
                while(a[--j]>pivot){}
                if(i<j){
                    swap(a,i,j);
                }else{
                    break;
                }
            }
            swap(a,i,right-1);//把三数取中值时放在right-1上的枢纽元放到i的位置
            quickSort(a, left, i-1);
            quickSort(a, i+1, right);
            /*
             *  如果33行~42行代码写成如下，则会可能由于a[i]=a[j]=pivot,即都等于枢纽元的情况而产生无限循环
             * 
             *  int i = left+1,j=right-2;
                for(;;){
                    while(a[i]<pivot){i++;}
                    while(a[j]>pivot){j--;}
                    if(i<j){
                        swap(a,i,j);
                    }else{
                        break;
                    }
                }
             */    
        }else{
            InsertionSort.sort(a);
        }
    }
    public static void sort(int[] a){
        quickSort(a, 0, a.length-1);
    }
}
```
##### 时间复杂度：
快速排序的时间主要耗费在划分操作上，对长度为k的区间进行划分，共需k-1次比较
最好情况为$O(N \log N)$。枢纽元刚好位于中间，则每次划分使左右两个无序区间长度大致相等，则有点类似于二分法，此时总的比较次数O($N \log N$)。
最坏情况为$O(N^2)$。枢纽元始终是最小或最大元素，则每次划分全部元素在一边，另一边为空，此时总的比较次数为$O(N^2)$。
平均情况为$O(N \log N)$。证明略复杂，详细可参考《数据结构与算法Java语言描述》P205

##### 空间复杂度：
与递归时造成的栈空间使用有关。平均来说，递归树的深度为$ \log N$，所以空间复杂度为$O( \log N)$
##### 稳定性分析：
假设两个相等的数大于枢纽元且均位于左边，则等左的数会比右边的数先填到右边的坑里，排序完成后相对位置改变，所以是不稳定的

#### 桶排序BucketSort/基数排序RadixSort/计数排序CountingSort
##### 介绍：
这三种排序都使用了桶的概念，即不比较而是通过分配再合并的方式进行排序，只是分配的方法不一样。
在计数排序中，每个桶存储一个特定的值，扫描待排序数组，值对应的放进桶中，最后将桶中元素倒出，最后数组有序
在桶排序中，给每个桶划定一定的范围，扫描待排序数组，将每个数放在对应范围的桶里面，再分别对每个桶排序（插排、快排等），最后将每个桶里面的元素有顺序的倒出，最后数组有序。
在基数排序中，对应元素的不同位数，如整数元素就可以对其百位数、十位数、个位数；字符串就可以对其不同位数上的字母，分别使用桶排序。

这几个使用桶概念的算法，都需要对待排序数组的情况有一定了解，例如数据量太大，就不适合使用计数排序，因为桶分配太多会浪费资源；数据间隔大但数据少，使用桶排序则不好划分范围等。但是就不代表不要用这些排序，事实上，如果选择合适，还是可以使用这些算法思想进行排序的，因为这几个排序是为数不多的可以以线性时间O(N)进行排序的算法。

关于更多内容可以参考博客[十大经典排序算法|JavaScript描述](http://damonare.github.io/2016/09/16/%E5%8D%81%E5%A4%A7%E7%BB%8F%E5%85%B8%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%E6%80%BB%E7%BB%93%EF%BC%88javascript%E6%8F%8F%E8%BF%B0%EF%BC%89/)中这几个排序的相关内容。

#### 交叉比较总结
参考博客：[八大排序算法](http://blog.csdn.net/hguisu/article/details/7776068)
##### 时间复杂度：
一般而言，
$O(N^2)$型的有：冒泡排序、选择排序、插入排序
$O(N \log N)$型的有：快速排序、归并排序、堆排序
$O(N)$型的有：基数排序、桶排序、计数排序

但是特殊情况，根据不同算法时间复杂度所依赖情况的不同，如输入基本有序时，则会让插入排序以及改进的冒泡排序节省大量比较次数而变得更快$O(N)$，但是却让以首元素作为枢纽元的快速排序变得很糟糕$O(N^2)$。除此之外，输入的情况对选择排序，堆排序，归并排序还有基数排序影响不大。

##### 空间复杂度：
冒泡排序、选择排序、插入排序、堆排序、希尔排序的空间复杂度均为$O(1)$
而快速排序与归并排序由于使用递归、其空间复杂度不仅依赖于额外空间的使用，还依赖于递归树的深度。

##### 稳定性
稳定的排序算法，冒泡、插入、归并、基数
不稳定的排序算法，选择，快速，希尔，堆

##### 一些排序算法的选择准则
对输入的考虑因素：
- 数据记录条目N的大小
- 数据记录的结构与分布情况（如关键字的间隔、是否有序以及相等的个数等等）
- 内存空间的要求
- 稳定性要求

因此，
当N较大时，可以考虑$O(N \log N)$型排序
一般使用快速排序（目前基于比较的内部排序算法中最好的），其次如果不要求稳定性的可以使用堆排序，要求稳定性但内存空间允许的情况下基本使用归并排序
当N较小时，可以使用一些简单的算法，如冒泡排序、选择排序、插入排序。
如果了解输入情况（如输入有序等），可以使用插入排序或改进的冒泡排序
如果不了解输入情况且不要求稳定性的，一般使用选择排序
特殊情况下还可以尝试基数排序、桶排序等

### 后记
上述内容仅为学习总结，如有错误希望能够指出，也希望有人不吝赐教

