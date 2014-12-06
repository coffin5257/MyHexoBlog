title: ISCC 2014 Writeup
tags:
  - CTF
  - writeup
id: 39
categories:
  - CTF
date: 2014-06-20 14:00:58
---

好多天前开始写的，本来打算写完再发出来，还是先发出来吧。
本文持续更新……2014.6.20

## 0x01 比赛介绍

ISCC 2014 比赛全称是“第11届北京理工大学信息安全与对抗技术大赛”。
时间是 5.1 8:00——6.14 24:00 。
题目大概是这个样子：

<img src="http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-writeup-01.png" width="70%" height="70%">

非常有意思吧！
<!--more-->
我是在5月底去北京，朋友跟我说了我才知道这个比赛的。

回来断断续续地做了一些，限于目前水平、经验不足，所以没做多少题。写这个主要是记录下自己的成长历程，如果能帮助到你那是再好不过了。

**截止到结束我做出来的题目：**

![iscc-2014-writeup-02](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-writeup-02.png)

## 0x02 Basic 关卡

### 兵者诡道

![iscc-2014-兵者诡道](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-problem-basic-01.png)

Writeup：
  抓包，key在返回包的 Header 里面
  p.s 发现没有，题目描述还是有点相关性的哈哈～

![iscc-2014-兵者诡道-Writeup](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-writeup-basic-01.png)
Key：`Welcome-to-ISCC`

### 知己知彼

![iscc-2014-知己知彼](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-problem-basic-02.png)

Writeup：
这是一道密码题：
`4545 424545 454542 454542 42 424542 424545`
不难想到 ASCII 码，45对应的是 - ，45 对应的是 * ，所以翻译出来就是：
`-- *-- --* --* * *-* *--`
就这么提交当然是错的，不能这么简单...这个看起来是不是像摩尔斯电码：`MWGGERW`

这么提交当然还是不对，那我们继续考虑下一层加密是什么？
对于这种纯数字密码，首先考虑底当然是凯撒密码（移位密码），其实很容易猜到，MWGG 对应 ISCC，那么后面底就是 ANS，提交 ISCCANS(忘了大写还是小写) 即可通过。

附：基本移位密码枚举脚本

### 正则指令

![iscc-2014-知己知彼](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-problem-basic-03.png)

Writeup：
解析得到这个：https://www.youtube.com/watch_popup?v=5x1vNTjbwcs&amp;List=PL3ZQ5CpNulQm1cXMJ5M6tX3O5vyXnCYFd
Key：`Chile hit by an 8.2 magnitude earthquake`

参考：[正则表达式30分钟教程](http://deerchao.net/tutorials/regex/regex.htm)

### 搜索情报

![iscc-2014-知己知彼](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-problem-basic-04.png)

我承认这题坑到我了...开始怎么都没找到，看评论里说的、以图搜图，得到的结果都是错的：http://www.welivesecurity.com/2014/02/11/windows-exploitation-in-2013/
后来有人在评论里发了出来：`No. It's named CFF Explorer`

提交 CFF Explorer 即可.

### 指令暗战

![iscc-2014-知己知彼](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-problem-basic-05.png)

Writrup：
用 Windows 的 debug 工具，-a 输入汇编指令，-u 查看机器码：
![iscc-2014-知己知彼](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-writeup-basic-05.png)

Key：`8CC88ED88EC0B92000BA0410BB0C00B80023`

### 巧入敌营

![iscc-2014-巧入敌营](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-problem-basic-06.png)

Writeup：
进去之后发现是两个文本框，点击提交就跳转到 aa.php 页面，表单提交是 Get 方式。
“技巧”就是：把 get 改成 post 。
  ![iscc-2014-巧入敌营](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-writeup-basic-06.png)

key：`4qrPccxPe9`

### 知兵之将

![iscc-2014-知兵之将](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-problem-basic-07.png)

Writeup：
  直接执行什么都没有，直接丢到 IDA pro 里面，在 main 函数里发现这个(我猜题目本意应该不是这个)：
  ![iscc-2014-知兵之将](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-writeup-basic-07-1.png)

update：Linux 有个`strings`命令，大概是查询字符串的：
  ![iscc-2014-知兵之将](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-writeup-basic-07-2.png)

Key：`abc456_09876tiyouare`

### 虚实密文

![iscc-2014-虚实密文](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-problem-basic-08.png)

Writeup：
文件下载下来是这个
![iscc-2014-虚实密文](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-writeup-basic-08-1.png)
这是肖申克底救赎里面的一句话，不过这不是重点。仔细看图，会发现有两种字体，这是培根密码：[维基百科.培根密码](http://zh.wikipedia.org/wiki/%E5%9F%B9%E6%A0%B9%E5%AF%86%E7%A2%BC)
  我们把正着的当作A，斜着的当作B，5个一组：
`AABAB BAAAA AABAA AABAA AAABB ABBAB ABABB`

Key：`freedom`

### 经之五事索其情

![iscc-2014-经之五事索其情](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-problem-basic-09.png)

Writeup：
很简单的一个 [RSA 算法](http://zh.wikipedia.org/wiki/RSA%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95) 的计算。给的n很小，而且其中一个因子都给出来了(43)：
$n= 2537 = 43&#42;59$
接着计算：
$φ(n) =(p-1)&#42;(q-1)=442&#42;58=2436 $
已知条件：
$e=13$
$c=981$
我们要做的就是计算私钥 $d$。
$d$ 和 $e$ 满足这个关系：$d&#42;e &#92;equiv 1 &#92;pmod {φ(n)}$
不难得到一个 $d=937$，然后按照$ P=C^d &#92;mod n $就可以算出密文。

Key：`704`

## 趁虚而入

![iscc-2014-趁虚而入](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-problem-basic-10.png)

Writeup： 
解压开是一个 handshake.cap 文件：
![iscc-2014-趁虚而入](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-writeup-basic-10-1.png)

用 EWSA 打开 cap 文件跑密码：
![iscc-2014-趁虚而入](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-writeup-basic-10-2.png)
p.s 重现是在虚拟机里，用的未注册版本的 EWSA ，所以密码没全部显示。

Key：`zzzzzzzz`

### 出其不意

![iscc-2014-趁虚而入](http://coffinblog.qiniudn.com/upload/2014/06/iscc-2014-problem-basic-11.png)