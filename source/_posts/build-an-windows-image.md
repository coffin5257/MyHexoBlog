title: Windows系统封装的那些事
tags:
  - 系统封装
  - 虚拟化
id: 14
categories:
  - 操作系统
date: 2014-03-06 14:00:29
---

## 声明：

本文不会手把手教，只是描述要点。
<!--more-->

## 为什么要封装、定制系统

无非是这些原因：

*   经常装机，每次都重新装一大堆驱动、配置一对软件太麻烦；
*   在1的条件下：我不想用网上别人封装的系统（不安全/用别人的掉份儿）；
*   电脑太渣（虚拟机），原版系统/已有系统 太大，占资源、占内存太多；
*   我闲的蛋疼，我是装逼犯；

## Ghost 介绍

Ghost是一款硬盘备份工具，也就是说它把你的 C 盘整个地打包起来，然后你要重装的时候把备份好的“解压”进去就能跟以前一样了，也就是“克隆”。

只是在XP和Vista时代用的比较多，一是XP的安装过程好长好长，批量部署安装实在是鸡肋；二是Vista好大好大，Ghost压缩很强悍；

所以，想做Ghost，网上资料一堆堆……

## PE 系统介绍

PE ( Preinstallation Environment )，从英文单词来看，也就是安装系统之前的环境。

实际上也就是个精简的系统，带有一些基本的功能，比如：分区、引导、Ghost……

根据内核，可以有 XP PE 、Win 7 PE 、Win 8 PE 、Linux PE 、 Mac PE …… 也就是说这些系统都可以精简到做一个PE系统，其实就是个小型的维护用的系统；

关于 Windows PE 的详细可以看 Microsoft 的官方介绍：[什么是 Windows PE ?](http://technet.microsoft.com/zh-cn/library/cc766093%28v=WS.10%29.aspx)

## Wim 和 VHD

到了Win7时代，系统安装已经很容易了，基本就是下一步下一步 … 我们先来看看我们从网上下载的 “原版” ISO 镜像里是什么样子的 ( 可以用WinRar、UltraISO等打开)：

![Windows ISO目录](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-1.png)

这个是我刻录好的，原版是没有 WinReduer81.wccf 和 HEU_KMS_Activator_v7.8.3.exe 这俩文件的。

打开 sources 文件夹就会发现一个 install.wim 文件，这个文件的大小基本占了整个镜像95%以上。也就是说实际上系统安装的主要资源都在这里面了，而我们今天要说的非 Ghost 封装系统，实际上就是针对这个 wim 文件的修改。

wim 文件是 Windows 镜像文件格式，可以挂载，可以拿来装系统。

至于 VHD ，它是一种虚拟硬盘，从Win7时代开始的。

玩过虚拟机的应该知道虚拟硬盘是怎么个东西，这里演示一下：

右键 计算机 &gt;&gt; 属性 &gt;&gt; 磁盘管理

![VHD演示1](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-2.png)

点击右上角的 更多操作 &gt;&gt; 创建VHD，输入基本信息

![VHD演示2](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-3.png)

创建完成后，就会发现出现了第二个磁盘 图中的“磁盘1”

![VHD演示3](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-4.png)

在磁盘1上右键 &gt;&gt; 初始化磁盘

![VHD演示4](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-5.png)

初始化完了之后，就是分区的过程了，这个和传统的磁盘分区操作一样

![VHD演示5](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-6.png)

看看操作完之后，出现了一个 Z 盘，就是我们的 VHD 虚拟硬盘的分区（这里只分了一个）
VHD演示6
看我们之前创建 VHD 时填的文件路径，里面出现了个 vhd 文件

![VHD演示7](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-7.png)

总之VHD就是虚拟的一块硬盘，基本上你在真实硬盘上能做的在虚拟硬盘里也可以做，比如：**把操作系统装进去**

## Windows 7 封装

概念区分：

*   联机封装：联机就是你的操作(打补丁、装驱动)都是在一个你装好的系统里进行的，等你把你觉得该装的东西都装好了，将其封装起来就是一个DIY的系统了；
*   脱机封装：脱机就像一个黑盒子，你只需要在原系统安装文件上进行修改，你知道你的修改回产生什么效果，但你看不到；

拿做网页来类比就是：
联机相当于你拿 Dreamweaver 拖动模块，还能不断调试，所见即所得；脱机相当于你拿记事本从开始一个个标签往下写，你知道这些标签应该是什么样子 ( 但可能由于某些原因它没有这样 )

脱机封装：傻瓜式工具 rt_7_lite ，[看这里](http://hi.baidu.com/zbezj/item/7df186ad29a0d89514107353)

联机封装：用虚拟机即可 ，[看这里](http://hi.baidu.com/zbezj/item/42afd4093033151bacdc7053)

## Windows 8/8.1 封装

工具：WinReducer、Windows ADK工具、ImageX、DISM …… [看这里](http://sphrbeu2012.blog.163.com/blog/#m=0&amp;t=1&amp;c=fks_084070085084082064085084087095086086088065086094086068080)

### 画外音：

如果你只想做一个不错的系统，并刻录进光盘使用——那这些可能不太适用于你。

相信我，一个DVD光盘大小是有限制的，补丁、驱动……balabala的一堆塞进去，可能就爆了，对系统组件不熟悉不知道哪些能删哪些不能删啊，虚拟机装起来好慢的啊……(￣▽￣)”

于是，我想到了以前在虚拟机里装的Windows Thin，然后我就发现了这个……

## Windows Embeded

embeded 是指嵌入式，总之就是微软为了嵌入式设备而发布的产品，比如医疗行业、制造业的一些设备。既然是给嵌入式设备用的，那么肯定是可以自定义精简的：[Microsoft Windows Embeded](http://www.microsoft.com/windowsembedded/zh-cn/windows-embedded.aspx)

只要注册就可以获得评估版的 Key，这也是通过合法途径使用 Windows 的方法吧。

来一个 Windows Embeded Standard 7 在虚拟机里的安装过程：

我们选择 Install an Image ，可以看到这里也有制作 应答文件 以及 Wim 镜像 的选项；

![Windows Embeded Standard 7演示1](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-9.png)

我们的目的是需要一个在小内存的虚拟机里运行流畅的系统，所以选择Thin Client（其它的模板大家可以试试），当然也可以不选择模板自己定制。

![Windows Embeded Standard 7演示2](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-10.png)

选择语言等设置，初步安装的只有这6种语言，我们选择 English

![Windows Embeded Standard 7演示3](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-11.png)

下一步：这里可以看到 Thin Client 包含的驱动和功能，勾选下方的 Modify ，这意味着我们可以在这基础上调整

![Windows Embeded Standard 7演示4](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-12.png)

这里选择自动就好，.pmq是计算机硬件配置文件

![Windows Embeded Standard 7演示5](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-13.png)

下一步直到这里，我们把 Fonts 里面需要的勾选上，比如 Chinese simplified 、Chinese traditional、Japanese 没有的话看对应语言的网页会乱码

![Windows Embeded Standard 7演示6](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-14.png)

同时把 International 里面需要的勾选上 比如 IME(输入法)支持、Language Pack Setup(语言安装支持、有这个组件就可以安装中文包) 其它的组件请自行选择。选好之后点击左下角的 Resolve Dependencies 检查依赖，如果有需要的组件你没选择会提示你的。

![Windows Embeded Standard 7演示7](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-15.png)

然后下一步下一步……就跟平时装系统一样了

![Windows Embeded Standard 7演示8](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-16.png)

这是我装完一些软件（Firefox QQ WinRAR 输入法等）之后的C盘大小

![Windows Embeded Standard 7演示8](http://coffinblog.qiniudn.com/upload/2014/03/build-an-windows-image-17.png)

## 总结

*   对于封装的详细过程，有更好的教程，所以我就不写了，只是把它们贴出来；
*   推荐资源：远景论坛；
*   系统装得好，备胎当到老；