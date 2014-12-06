title: Google Hacking 教你找女神
tags:
  - Google Hacking
  - 搜索引擎
  - 社工
  - 隐私
id: 27
categories:
  - 网络安全
date: 2014-04-21 14:00:31
---

## 0×00 硝烟四起

大概是早上我转了这样的一篇文章:[《周末阅读：当黑客邂逅爱情》](http://www.freebuf.com/articles/others-articles/32389.html)

好像这篇文里的女神暴露的信息有点多了，下午Freebuf上立刻就有大神出来打抱不平：
[《当黑客邂逅爱情：不作死就不会死》](http://www.freebuf.com/articles/others-articles/32551.html)

此文详细介绍了作者跳出来反社工原文作者的经过，他还透露了女神确实很漂亮。看来都是女神惹的货，看得我等小白简直要按捺不住了……

想试试？别急，我先来给大家介绍下社工中必不可少的一个环节——搜索。
<!--more-->

## 0×01 度娘的秘密

毫无疑问，黑客们都喜欢用高贵冷艳的 Google，但是度娘也不是一无是处。比如，度娘对中文的智能语义化处理就比 Google 强多了。另外，度娘的贴吧、网盘可都是好资源，藏着巨大的秘密。

比如，上文的两篇社工文里就充斥着百度贴吧的搜索内容，全吧搜索和吧内搜索都是很强大的东西呢。

口味完全不一样啊！
![google-hacking-1](http://coffinblog.qiniudn.com/upload/2014/04/google-hacking-01.png)

## 0×02 Google——我有特殊的打开方式

相信大家都知道，Google的很多服务在大天朝都是用不了的，所以Google的搜索很不稳定，这也是很多人不喜欢用 Google 的原因。

但是Hackers怎么能没有Google，所以我们一般都要用正确的姿势来打开Google，比如：VPN、代理、G0Agent……

这些姿势大家应该都会。

## 0×03 Google 基本使用

最基本的当然是设置项了，也就是偏好设置（Preferences），这里包括了一些语言设置、显示设置。
然后我们可以开始使用了。

**这是一些基本规则：**

*   Google 不分大小写
*   Google 可以使用通配符：*表示一个词/字
*   Google 会智能地保留一些内容，比如一些过时的词，一些不适合呈现的内容（比如违法信息）
*   最常用的："关键字"，双引号会使Google强制搜索包含关键字的内容
*   布尔操作符：AND(+) NOT(-) OR(|)，AND 现在已不需要，多个关键字google会都匹配到

搜索无效不要紧，要注意看Google给出的提示。

**Google高级搜索：**

Google 有一个高级搜索（Advenced Search）的功能，可以设置更多的搜索条件以得到更为精确的内容。
这些条件最终都会反映到搜索界面的url里，所以熟悉url参数显然能给我们带来更好的搜索体验。
一个最基本的例子就是：

www.google.com.hk/search?q=jizemingbu

把这个url复制到浏览器访问里就能得到搜索结果，这里q=jizemingbu 就是一个参数，参数之间是用&amp;连接的，汉字、空格等不用担心，会被自动处理的。

但假如我们在搜索框里直接输入 jizemingbu，然后搜索，url会是这样：

www.google.com.hk/webhp?hl=zh-CN#hl=zh-CN&amp;newwindow=1&amp;q=jizemingbu&amp;safe=strict

url里有一些关键信息，比如语言设置，安全搜索等，这些都可以到高级搜索里调整。测试发现现在google的url参数已经复杂的多，简单的修改参数不一定生效，比如将 safe=strict 改成 safe=off 没有达到想要的效果。

## 0×04 高级操作符

前面的只是很基础的，到这里就要上重头戏了。
比如前些天的openssl漏洞，怎么大范围获取 https 的站呢？

![google-hacking-2](http://coffinblog.qiniudn.com/upload/2014/04/google-hacking-02.png)

除了 inurl ，一般常用的还有：

intitle allintitle 页面标题里查找关键字
filetype 查找指定文件类型 等同于 ext
site 锁定站点，比如 Freebuf 的搜索就是这样
inurl allinurl 在url中查找关键字
intext allintext 网页内容查找关键字
link 搜索链接到某个网站的链接

*   基本使用方法就是`操作符:关键字`，要注意这三者之间是不能有空格的，如果关键字里有空格就得用引号包起来。
*   带all的操作符貌似不能跟其他的关键字一起用
*   遇到搜不出来结果的，注意看Google的提示或者调整下关键词顺序

**来几个简单的例子：**

![google-hacking-3](http://coffinblog.qiniudn.com/upload/2014/04/google-hacking-03.png)

![google-hacking-4](http://coffinblog.qiniudn.com/upload/2014/04/google-hacking-04.png)

> p.s 一些高级操作符组合起来威力更大哦~

## 0×05 开始Hacking

前面学习了这么多姿势，现在该来实战看看了。

**最基本的一个小技巧，查看快照——不只是Google的。**

比如 http://www.freebuf.com/tools/29161.html 这个停止分享了，去搜一下，查看快照就有了。现在我这里Google的快照打不开，但是百度的可以。

**服务器信息收集**

比如 `intitle:index.of filetype:log`
通过这个还能找到一些搜不到的目录，比如通过搜索进入了某个文件目录，由于服务器设置不当，通过 “上一级” “ParentDirectory”可以跳到别的目录。

此外,还可以在指定站点搜索一些常见的错误信息，比如 `intitle:error intitle:warning`
**给大家列一些常用的关键字：**

intitle:index.of
error|warning
login|logon
username|userid|ID
password|passcode
admin|administrator
-ext:html|-ext:htm|-ext:asp|-exp:php 过滤掉常见的
inurl:temp|inurl:tmp|inurl:backup|inurl:bak 这个不一定用url来

## 0×06 结束前想说的

这两天正好是想要总结下Google Hacking ，只是今天正好发生了这么有趣的事情。我知道这些东西网上有很多，但自己写才能有自己的收获。

这篇介绍的只是很基础的，更高级的应该是利用一些 Google 的 API，写脚本，实现信息收集的自动化。

或者使用一些特殊的搜索引擎：
黑暗Google：[SHODAN](http://www.shodanhq.com/)
钟馗之眼：[ZoomEye](http://www.zoomeye.org)