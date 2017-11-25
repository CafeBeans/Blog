---
title: 跳出舒适区-入坑Ubuntu折腾记
date: 2017-11-20 23:21:20
categories: 实践记录
tags:
- Ubuntu
- Linux
- 工具
---
最近朋友打算把windows换成了linux当做主力系统来用，自己也一时心血来潮，折腾了几天，多次重装配置后终于把ubuntu折腾成合心意的样子。其实这并不是我第一次装Ubuntu，大概一年前左右也抱着一个想学习linux的伟大理想，在电脑里装了双系统，只可惜那时候对linux的认识近乎为0，装完了没几天因为难操作就再也没有碰过了，久而久之也就顺手删掉了装有Ubuntu的磁盘分区，放下了执念。后来对linux的学习，就停留在鸟哥的书中以及虚拟机里，以及一些云服务器的远程操作。由于近来在刷实验楼（在线编程网站），在实验环境中熟悉了一把linux操作，再加上对linux已经有了一定的了解，在朋友的刺激下，就重新开始了折腾，不过这次是认真的。
<!-- more -->
### 下载与安装
关于下载和安装步骤，随便在网络上搜一下就能出很多的教程，由于经常性的装系统，基本不需要参考，娴熟地直接去[官网](http://cn.ubuntu.com/download/)下载Ubuntu镜像和制作好U盘启动盘后，了解下Linux分区注意事项后就顺利的装上了Ubuntu16.04LTS版本。具体过程就不过多记录了，不过对于Linux的文件系统、目录结构、磁盘分区等相关概念，还是需要留点心思的，因为在安装过程中可以自行创建分区,并且指定分区挂载在哪个目录上,了解这方面有助于做好这一步的决策。

#### Linux虚拟文件系统
>好文推荐阅读
[比起Windows，怎样解读Linux的文件系统与目录结构？](http://www.infoq.com/cn/articles/how-to-read-linux-file-system-and-directory-structure)

文件系统,解决的是文件在硬盘中的管理问题,还没学linux之前,我所理解的文件就仅限于文档,图片,视频,还有软件数据文件和系统文件等等一切可以在CDEF盘中看到的东西,处于先有硬盘然后分区格式化产生文件系统最后才能管理文件的回路中,而linux则刷新了认知,一切皆文件,甚至在windows概念中统治着文件的磁盘以及其他奇奇怪怪的东西，都只是linux的一个文件而已。
这种一切皆文件逻辑的实现，依赖于虚拟文件系统的设计。在linux系统启动过程，内存会先加载具备文件管理，设备管理，内存管理，CPU进程调度管理，网络管理等功能的linux内核，然后内核在内存中建立虚拟文件系统，也就是以/根目录开始的目录树，进而挂载各种资源（硬盘，键盘，鼠标，数据，程序，CPU，内存，网卡等）到特定目录中，以文件的方式进行管理。不得不说Linus真的强，让linux内核仅仅保留管理软硬件资源的能力，其他则让我们在虚拟文件系统上自由发挥，为所欲为。
在虚拟文件系统之下，还延伸出一套定义linux操作系统主要目录及目录内容的标准——文件系统层次结构标准（英语：Filesystem Hierarchy Standard，FHS）

#### FHS-文件系统层次结构标准
>[维基百科](https://zh.wikipedia.org/wiki/%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F%E5%B1%82%E6%AC%A1%E7%BB%93%E6%9E%84%E6%A0%87%E5%87%86)

![FHS](https://dn-anything-about-doc.qbox.me/linux_base/4-1.png/logoblackfont)

FHS是随着时间和使用经验不断发展的，在日后可能也还会不断修改，因为linux是一个允许多用户同时操作的系统，不像windows（我们在操作别人电脑时是不是经常由于不熟悉，不知道文件放在哪里而花费时间在找文件上）。在linux中，只要大家都遵循FHS，大家对同一台主机就会有一个共识，知道哪些文件能碰，哪些不能碰，甚至面对所有linux主机都能很快上手工作，而问题更多的出现在用户权限上。所以FHS标准是一个解决多用户使用系统时相互干涉问题很好的手段。

#### 目录与分区策略
在了解各目录之后，就可以根据用途设置相应的分区策略了。详细可参考[linux分区方案](http://www.jianshu.com/p/f229cf403836)

### 一些完成安装后的设置(收集整理)
#### 系统设置
- 让Ubuntu使用本地时区，修复双系统时间显示不一致
> timedatectl set-local-rtc 1

- 显卡驱动设置：系统设置>软件和更新>附加驱动>使用nvdia专有驱动

- 更换软件源
>软件源告诉系统获取软件的位置,除了Ubuntu官方软件源及国内镜像外，还有PPA独立开发者软件源
[软件源地址及更换方式](http://blog.csdn.net/wuzuodingfeng/article/details/76155829)
[详解Ubuntu软件源](http://www.jianshu.com/p/57a91bc0c594)

#### 桌面美化
##### 更换为gnome
>安装gnome-shell的时候选择lightgm，不然开机默认还是unity
sudo apt-get install gnome-shell
sudo apt-get install ubuntu-gnome-desktop

一开始使用自带unity桌面，时不时卡顿，后来更换gnome桌面几乎没有卡顿现象,而且特别喜欢，然后就顺手[删掉了unity桌面](http://www.playubuntu.cn/article/110.html)

##### 主题和图标
- 安装Flatabulous主题
>sudo add-apt-repository ppa:noobslab/themes
sudo apt-get update
sudo apt-get install flatabulous-theme

- Flatabulous主题的配套图标
>sudo add-apt-repository ppa:noobslab/icons
sudo apt-get update
sudo apt-get install ultra-flat-icons

- 安装gnome-tweak-tool，设置主题和图标
>sudo apt-get install gnome-tweak-tool

- Docky，类似mac的dock栏
>sudo add-apt-repository ppa:docky-core/stable
sudo apt-get update
sudo apt-get install docky


#### 软件管理
必须清楚，linux有众多发行版，例如redhad、debian等等派别，在软件包管理上有着不同的方式，ubuntu属于使用apt包管理的debian系。在尝试过想要删除源码安装的软件由于无法执行make uninstall后需要根据安装日志删除不同位置的文件时，我就彻底决定能用apt或dpkg管理软件则优先使用这些工具了。

##### 删除自带软件
>删除软件及配置文件：
sudo apt-get remove --purge thunderbird totem rhythmbox empathy brasero simple-scan gnome-mahjongg aisleriot gnome-mines cheese transmission-common gnome-orca webbrowser-app gnome-sudoku landscape-client-ui-install onboard deja-dup libreoffice* unity-webapps-common
清除依赖：
sudo apt-get autoremove

- thunderbird 邮件客户端
- totem  电影播放器
- rhythmbox 音乐播放器
- empathy 聊天软件
- brasero 光盘刻录工具
- simple-scan 扫描器
- gnome-mahjongg 对对碰游戏
- aisleriot 纸牌
- gnome-mines 扫雷
- cheese 相机
- transmission-common BT客户端
- gnome-orca 屏幕阅读
- webbrowser-app 自带浏览器
- gnome-sudoku   数独
- landscape-client-ui-install  管理服务
- onboard 屏幕键盘
- deja-dup 备份
- libreoffice* 自带office套件
- unity-webapps-common 亚马逊链接

##### 安装常用软件(个人使用)
- [浏览器：chrome](http://www.linuxidc.com/Linux/2016-05/131096.htm)
- [输入法：搜狗输入法](http://blog.csdn.net/leijieZhang/article/details/53707181)
- 办公：wps
[deb包下载](http://wps-community.org/download.html)
[字体缺失解决方案](https://my.oschina.net/renwofei423/blog/635798)
[中文输入设置](http://blog.csdn.net/github_34832841/article/details/51850731)
- 音乐播放器：网易云音乐
[deb包下载](https://music.163.com/#/download)
- [下载工具：uget+aria2](https://www.yuanmas.com/info/EkyQZPg3Ov.html)
- [文本编辑：notepadqq](https://www.linuxdashen.com/install-notepadqq-debian-ubuntu-arch)
- vim
>sudo apt-get install vim

- git
>sudo apt-get install git

- [JDK](https://zhuanlan.zhihu.com/p/27892768)

- IntelliJ IDEA
[安装包下载](https://www.jetbrains.com/idea/download/#section=linux)
[安装教程](http://wiki.jikexueyuan.com/project/intellij-idea-tutorial/ubuntu-install.html)

等等根据实际需求安装

### 使用体验
用了几天的Ubuntu，单从桌面环境来说的话其实和用windows区别不大，大部分windows下的场景都能满足，只是由于刚开始不太习惯而且不熟悉导致花了较多时间在解决问题上面。幸好ubuntu的粉丝还比较多，在官方问答社区[AskUbuntu](https://askubuntu.com/)或者善于利用Google都能找到不少问题的解决方案，减少自己折腾的时间。
折腾完之后的系统基本能用了，但是离好用还有很大的距离，因为命令行操作才是linux系统高效解决问题的精髓，也是图形化之外的一大杀器，如果单纯为了用桌面和界面美化，那还不如回到windows的怀抱，根本不需要折腾闹人的linux。有好多人持有一种真的要学linux的话就不要装图形化界面，直接进行终端操作的观点，这种观点也是可以理解的，因为没有界面就不得不练习终端操作，只是过程非常的痛苦，很多时候因为不熟悉浪费更多的时间，到头来折腾了很久整个系统连能用都算不上，学习的曲线比较曲折。个人觉得倒不如先安装个有图形化界面的linux发行版保证能日常使用（这个比较推荐Ubuntu，有良好的社区支持、软件支持），然后日常逐步用命令取代点鼠标的操作，因为命令操作讲究的是熟悉度，一条命令打一半查一下是谈不上高效的，待熟悉了之后再逐步过渡到纯命令行操作，进入无鼠标的境界（有点久远）。不过这也就有一个问题，能否养成一种意识：在每次点鼠标之后都能想到去找相应的命令完成同样的任务并且接下来都不再使用鼠标，而这也应该是刻意练习的哲学吧。[linux命令大全](http://man.linuxde.net/)这个网站很好的收录了linux命令，剩下的就靠自己的造化了。
最后附一张桌面图：
![](/images/screen.png)












