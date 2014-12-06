title: KCon V3 见闻
tags:
  - Rop
  - XSS
  - 木马
  - 黑产
id: 111
categories:
  - 网络安全
date: 2014-08-24 08:30:20
---

<img src="http://coffinblog.qiniudn.com/upload/2014/08/KCon-V3-2014-show.jpg" width="50%" height="50%">
开场有表演.
去晚了，没找到好位置，只好站在旁边了。
开场预热了一会就进入正题了。
<!--more-->
这是他们的时间表：

![KCon V3时间表](http://coffinblog.qiniudn.com/upload/2014/08/KCon-V3-2014-timeline.jpg)

下面是各个议题（位置不好没怎么拍照，以下图片来自官网 kcon.knownsec.com）：

### 去年跨过的客户端（演讲者：Evi1m0）

![去年跨过的客户端](http://coffinblog.qiniudn.com/upload/2014/08/KCon-V3-2014-clientxss.jpg)

这个主要是介绍一些客户端（实际也是Web）的 XSS ，除了 http 域 ，客户端用的很多的的还有 file 域，尤其是不同平台下的策略不一，从而可能导致一些跨平台的 WSL .

### ROPs are for the 99%（演讲者：tombkeeper）

![ROPs are for the 99%](http://coffinblog.qiniudn.com/upload/2014/08/KCon-V3-2014-rop.jpg)

感觉这篇是大干货，说实话，对于一个不搞系统的人，平时接触这方面比较少，这个演讲让我对 Rop 有了一个认识。
_科普一下：Rop全称Return-Oriented Programming，就是对栈上的返回地址进行利用的一种攻击方式_

这个演讲主要讲通过 IE 的 JScript 的一些利用，从而绕过防御得到内存地址，执行 shell code 。

### 利用 OAuth 劫持用户身份（演讲者：蓝色 di 雪球）

![利用 OAuth 劫持用户身份](http://coffinblog.qiniudn.com/upload/2014/08/KCon-V3-2014-oauth.jpg)

这个议题在 KCon 开始前就已经在知乎上预热的差不多了，当时有点困了也没认真听 ToT

### ZoomEye 新版本发布（余弦 &amp; zoomeye团队）

![ZoomEye 新版本发布](http://coffinblog.qiniudn.com/upload/2014/08/KCon-V3-2014-zoomeye.jpg)

参见 http://www.zoomeye.org 有 New 标记的.
比较直观的就是 Vision 功能了，截图看看：

![ZoomEye 网络空间可视化](http://coffinblog.qiniudn.com/upload/2014/08/KCon-V3-2014-zoomeye-vision.png)

### APT 攻击揭秘之特种木马技术实现（演讲者：冷风）

![APT 攻击揭秘之特种木马技术实现](http://coffinblog.qiniudn.com/upload/2014/08/KCon-V3-2014-trojan.jpg)

此议题先讲了讲了下制作稳定高效木马的思路，冷风也是潇洒的很。

### 藏匿在黑暗世界的利益链条（演讲者：redrain）

![藏匿在黑暗世界的利益链条](http://coffinblog.qiniudn.com/upload/2014/08/KCon-V3-2014-dark-industry.jpg)

说黑产的，中规中矩，由于公开演讲的原因，感觉爆的料还不够啊。

最后，最后！
抽奖，男科圣手 · 黑哥登场：
![黑哥](http://coffinblog.qiniudn.com/upload/2014/08/KCon-V3-2014-heige.jpg)

你别这么自黑好么 =_=

P.S:详细的还是等官方放出 PPT 再好好看看。