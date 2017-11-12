---
title: KMP算法解惑及代码实现
date: 2017-08-06 20:40:46
categories: 技术探究
tags:
- 算法
---
# 前言
最近在看左神的算法直播，其中讲了一个之前一直似懂非懂的算法——KMP，是一个在长字符串中找到匹配的模式串并返回模式串在长字符串的位置的算法，类似于常见的文本编辑软件所提供的Ctrl+F文本查找功能。早些时间看阮一峰的[字符串匹配的KMP算法](http://www.ruanyifeng.com/blog/2013/05/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm.html)，初步理解KMP算法就是通过一个next数组记录模式串中前缀与后缀的最大匹配值，并利用这个信息来控制跳转的位置，以优化暴力方法中出现的没有必要的比较过程。
在看了左神对KMP算法的解释后，决定梳理一下KMP算法，并且主要针对先前疑惑的几个点做一下阐释。
<!-- more -->
# KMP算法
由于阮一峰对KMP算法流程已经做了相当好的解释，在这里厚颜无耻的借用下其博客的部分内容（主要是图片），并且结合左神的思路梳理KMP流程（注：i为字符串的下标，初始为0；j为模式串的下标，初始为0）：

## 基本流程
### 流程图解
![](http://image.beekka.com/blog/201305/bg2013050103.png)
首先，长字符串"BBC ABCDAB ABCDABCDABDE"的首位字符（i=0）与模式串"ABCDABD"的首位字符(j=0)进行比较。因为B与A不匹配，所以模式串首位与长字符串第二位（i++）比较。

![](http://image.beekka.com/blog/201305/bg2013050104.png)
因为B与A不匹配，模式串首位与长字符串下一位（i++）比较

![](http://image.beekka.com/blog/201305/bg2013050105.png)
直到长字符串有一个字符，与模式串的首位字符相同为止。

![](http://image.beekka.com/blog/201305/bg2013050106.png)
接着比较长字符串和模式串的下一个字符（i++,j++），还是相同。

![](http://image.beekka.com/blog/201305/bg2013050107.png)
直到长字符串有一个字符，与模式串对应的字符不相同为止。

![](http://image.beekka.com/blog/201305/bg2013050108.png)
这时候，最简单粗暴并且容易想到的做法，就是将模式串从头开始和字符串回滚后的下一个字符比较（若pos为字符串与模式串首位相同的位置，则j=0，i=pos+1），也就是所谓的暴力枚举。显然，若字符串有n个字符，模式串有m个字符，那么时间复杂度为O(mn)，在数据量大的情况下是不可接受的

![](http://image.beekka.com/blog/201305/bg2013050107.png)
这时候，就轮到可以优化暴力枚举的KMP发挥作用了，其优化主要依赖于next数组,可以通过跳转到指定位置而不是从头开始来避免没有必要的比较

![](/images/next.png)
对应于模式串的下标，next数组记录着模式串中每一个字符前面的子串中前缀与后缀的最大匹配数值，如图为该模式串的next数组，具体介绍及计算方式下面会继续梳理。

![](http://image.beekka.com/blog/201305/bg2013050107.png)
当遇到不匹配的字符的时候(i=10,j=6)

![](http://image.beekka.com/blog/201305/bg2013050110.png)
模式串下标跳转到不匹配字符的next值2的位置，即j=next[6]=2。也就是说，字符串中不匹配的空格字符直接与"C"开始比较，其下标不需要回退

![](http://image.beekka.com/blog/201305/bg2013050111.png)
同理，模式串下标跳转到0位置，j=next[2]=0,继续比较

![](http://image.beekka.com/blog/201305/bg2013050112.png)
由于模式串首位也不匹配，于是回到流程一开始的地方，重复上述过程，直到完全匹配为止

![](http://image.beekka.com/blog/201305/bg2013050113.png)
完全匹配后，返回模式串在字符串中的位置，流程结束

### talk is cheap,show the code
```
	public int getIndex(String s,String m){
        //处理不合理输入
        if (s == null || m == null || m.length() < 1 || s.length() < m.length()) {
            return -1;
        }
        //初始化
        char[] schar = s.toCharArray();
        char[] mchar = m.toCharArray();
        int i = 0,j = 0;
        int slength = schar.length;
        int mlength = mchar.length;

        //获取模式串next数组
        int[] next = getNextArray(m);

        //KMP流程
        while(i<slength&&j<mlength){
            //若字符相同
            if(schar[i]==mchar[j]){
                i++;
                j++;
            }else if(j==0){//字符不同，且此时模式串下标在首位
                i++;
            }else{//字符不同，且发生在字符串比较中，需要跳转
                j = next[j];
            }
        }
        //流程结束，观察模式串是否已全部匹配完成
        return j==mlength?i-j:-1;
    }
```

## next数组详解
KMP的核心就在于next数组的计算，再次明确下next数组的意义，**next数组记录着对应位置的模式串字符前面最长相同前缀后缀长度**，看上去很拗口，其实也就是这么个东西

### 前缀与后缀
首先得理解前缀与后缀的概念，对于一个字符串abcde,前缀为a、ab、abc、abcd,后缀为bcde、cde、de、e。
也就是说，前缀就是从第一个字符开始往后延伸，但不包含最后一个字符的字符串，后缀就是从最后一个字符开始往前延伸，但不包含第一个字符的字符串
![](http://image.beekka.com/blog/201305/bg2013050114.png)

### next数组取值规则
在模式串ABCDABD中，next数组如下：
![](/images/next.png)
当j = 0时，前面没有任何字符，人为规定，其next值为-1，即next[0] = -1；
当j = 1时，前面的字符串为A，其最长相同前后缀长度为 0，即next[1] = 0；
当j = 2时，前面的字符串为AB，其最长相同前后缀长度为 0，即next[2] = 0；
当j = 3时，前面的字符串为ABC，其最长相同前后缀长度为 0，即next[3] = 0；
当j = 4时，前面的字符串为ABCD，其最长相同前后缀长度为 0，即next[4] = 0；
当j = 5时，前面的字符串为ABCDA，其最长相同前后缀为A，即next[5] = 1；
当j = 6时，前面的字符串为ABCDAB，其最长相同前后缀为AB，即next[6] = 2；

### next数组计算
在next数组中，除了公认的next[0]=-1、next[1]=0外，其余每个位置的值都可以基于前一位计算出的next值得出结果
![](/images/next计算.png)

假定pos为待求next值的位置，pos-1则为上一次求值的位置，根据next[pos-1]可以知道其前缀与后缀的长度，temp为其前缀的下一个位置。
显然，若temp位置上的字符与pos-1位置上的字符相同，则对于pos来说，其next值则在pos-1的next值基础上+1；
若字符不同，则可继续利用已经求得的next[temp]值，比较t位置的字符与pos-1位置的字符是否相同，若相同，则对于pos来说，其next值则在temp的next值基础上+1
如此反复，若每一次temp值都不匹配，最后得出next[pos]=0

*看上去会有点凌乱，只要记住，next值指示的长度中，前缀和后缀的字符是一样的就会清晰很多了*

### talk is cheap ,show the code
```
	public int[] getNextArray(String m){
        if(m.length()==1){
            return new int[]{-1};
        }
        int[] next = new int[m.length()];
        char[] mchar = m.toCharArray();

        //固定值
        next[0]=-1;
        next[1]=0;
        //从下标为2开始计算
        int pos = 2;
        //最长相同前缀末尾+1的位置，初始为0
        int temp = 0;
        while (pos < next.length) {
            if (mchar[pos - 1] == mchar[temp]) {
                next[pos++] = ++temp;
            } else if (temp > 0) {
                temp = next[temp];
            } else {
                next[pos++] = 0;
            }
        }
        return next;
    }
```

# 疑惑
## KMP优化过程的疑惑

### 疑惑一
![](/images/KMP图解1.png)
如图，s为字符串，m为模式串，假设字符都相同，当分别到达i,j位置时，字符不同，根据KMP的规则，模式串j跳转到j'的位置（相同前缀后缀的长度）继续与i位置的字符比较。为什么模式串可以直接跳转到j'位置继续与i位置字符比较，而不需要重头开始比较？
首先需要清楚，字符串与模式串到达i,j位置时才不相同，也就意味着其前面的所有字符都一一对应。根据next[j]可以得出m前缀与后缀是相同的，后缀又与s中后面的子串相同，m经过跳转后的m'在j'前面的子串又与m的前缀相同，因此图中四块圈起来的区域是相同的，跳转后j'之前的字符没有必要继续比较。
这是KMP其中一个优化。

### 疑惑二
![](/images/KMP图解2.png)
如图，完全匹配后，是否存在一个更早的a位置,使得字符串与模式串相同，换言之，完全匹配后的模式串一定要从b位置开始吗？
利用反证法，若存在一个a位置开头的模式串，那么在模式串m'中也必须存在一个a'，对应于a。这就使得j'前所表示的前缀变长，换言之，模式串中存在一个更长的相同前后缀，而原本next数组的值就是最长的相同前后缀，因此不符合要求，完全匹配的模式串的起始位置必定不早于b，因此b位置前的字符也没有必要继续比较。
这是KMP的第二个优化。

### 小结
因此，KMP优化过程中，免去了b位置之前不可能出现的情况以及b-i之间一定相同的情况的字符比较过程

## next数组的计算过程
### 比较对象的选择
![](/images/next计算.png)
在next数组的计算过程中，为什么一定要选pos-1位置的字符与temp、temp=next[temp]、……等等位置上面的字符相比较呢？
回想下next的定义——最长的相同前后缀长度，而前后缀分别始于第一个字符和最后一个字符，因此要计算后缀，必须得比较pos-1位置的字符。
![](/images/next计算2.png)
至于temp、t=next[temp]位置的选取，根据上图可知，依据pos-1的next值，我们可以发现下方前、后、前、后四块区域是完全一致的，因此在这个情况下要计算前缀则可以比较t与pos-1位置上的字符，以此类推……

# 小结
至此，KMP算法梳理完成