title: 第二届 360 杯全国大学生信息安全技术大赛总结
tags:
  - CTF
  - writeup
id: 33
categories:
  - CTF
date: 2014-05-27 14:00:33
---

嗯，没做出来几道题，所以只是个总结，不能说是 Writeup :-)

第一天，是 CTF ，类别有加密解密、网络协议、web攻防、数字取证、逆向分析。到这个比赛为止，其实也没参加几次CTF类的比赛，所以经验感觉上还是不足，比如对时间的把握，对题型思路的判断等。

一开始我是做 web，队友做逆向，还有一个妹子打酱油- -
<!--more-->
Web 第一题一看，第一反应是改成自己邮箱，发送一下验证码，去邮箱看了看没有，然后换成 gmail也是没有，以为题目的内网服务器比较堵啥的……然后思维就很浪了，还给 360-question@360.cn 发了个邮件试试有没有自动回复，结果也没有。
然后我去看了看这个邮箱其实不存在，于是我就想去注册一下这个邮箱，弄个自动回复，说不定还能坑到人……
**No zuo no die！** 找了一番我发现 360.cn 邮箱没有开放注册- -
在这个过程也有抓包，本来返回包也是有拦截的，比赛前觉得包可能很多容易干扰所以就取消了。然后后台的 check_rank 还在不停刷，导致我完全忽视了返回包……注意到才发现返回包里就有 key.

这次的 ctf 题目是分层的，1—5层，总分值超过下一层的分值才能开启下一层题目。所以做不了 web20 我就去做别的了，看了看加密解密 10 分题：

What这是啥- -完全没经验啊…

用一个脚本跑了跑啥密码，没跑出来……我竟然脑洞大的连里面的提示都没注意到…
队友这时也说，逆向不会……然后他就去做取证了，取证第一题倒是在我研究 解密10 的时候很快做出来了.
然后我又翻了翻网络协议：

额，好像知道大概怎么回事…可是，还是不会

这时候到午餐时间了，草草地吃了下饭，下午我注意到加解密10分的提示了:

> What is the key?The key is Rot13.

比赛前几天找了本《密码学与编码理论》看了看，但是好像没看到这个，Google 了下，发现是凯撒密码的一种，就是变换值是 13 ，比如 
A => N 
B => O
把那段奇怪的 js 代码解密了下……折腾了快俩小时，在Chrome控制台里跑出来后……提交竟然是错的！！！ 完全不懂为啥……
赛后问了问蓝莲花他们也没做出来啊呵呵呵呵呵…… :-)

在这个过程中还顺便跟队友讨论了下加解密20分题，是一张图，好像是改成zip解压吧，反正能得到一个txt文件，里面给了两个疑似 md5 的东西，说是解密后拼接在一起，再加上给的一个字符串提交就行了。

> 将 AAD3B435B51404EEAAD3B435B51404EE:DBDAAAC4D524F0DF9B34CCC255D061B5 解密后，与 975c692cef5f3726f86705cbfd5dae93 合并后作为通关密码.

小伙伴把这两个拿 md5 解密网站上去跑，没跑出来啥，然后拿去 Google 一下，第一个是这样：

> The string “aad3b435b51404eeaad3b435b51404ee” is the LM hash for ‘no password’. In other words, its empty.

没太懂，是空的？
然后纠结了一会，我把第二个拿去搜，最终在这里找到了 http://sha1.znaet.org/list/2141000
不是 md5 ，是 ntml ，明文是 360360 .
嗯，既然第一个是空的……那就不要好了，直接提交 360360975c692cef5f3726f86705cbfd5dae93 ，OK！这时候第三层也开了……

后面我仗着前几天看过一些图片取证的题……去尝试了下，没做出来又想起 web 后面的能做了，反正当时的状态挺乱的，看到 web 后面的题也没啥思路- -

然后就这么混结束了……

第二天！！！攻防战！！！
不得不吐嘈 :-)

一早上来给了4个虚拟机跳板，然后还有一个隐藏的最目标，总之就是拿下权限就给分。
队友决定分工，他先做第一个（访问是个 WordPress）。我挨个试了试，发现最后一个貌似是个 Windows + IIS 的，于是决定拿windows开刀！
扫了一下，发现开了一些端口，没啥头绪（经验太少）.
然后又去 Google 怎么 Hack Windows，也没找到有料的干货…然后差不多11点了（10点开始）.
这个时候小伙伴把主页查看源码，发现里面的一个奇怪的链接是个包含文件漏洞，是某个插件的，但他网上找了找，没找到啥利用方法。
我想到以前看到的 ACTF 的 Writeup，里面就有这种情况：
首先是可以试试 php 的伪协议：比如 data:// php://input ，但是这个需要服务器 php 设置的配合，这里好像不行；
然后我就尝试包含系统敏感文件了，之前发现它是 CentOS + Nginx，于是包含了 /usr/local/nginx/conf/nginx.conf，果然成功显示出来，通过这个找到了access日志文件，error日志文件，当然一门死脑筋想着制造错误日志，结果发现 error_log 是 crit 级别的，一般访问错误不会有记录 :-)

后来发现访问 access_log 会弹窗 :-) 我还记下来怀疑这是主办方提示…2333，同时也看到有童鞋在爆破后台密码。
然后我就意识到——access_log 不一样可以包含、解析么- -
但是我通过url插了一些 script 弹窗，刷新发现没出现- – 然后通过 user-agent 插了个一句话，小伙伴拿菜刀连上，成功上传大马！
然后我们就拿到数据库帐号、密码，把后台密码直接换成我们博客的，开始更新漏洞插件，通过 access_log 发现主题还有个任意文件下载漏洞，于是把主题换了。（说起来当时下载wp-config.php失败了还以为是被删了，后来意识到忘了 – 符号）
然后我们就很欢乐地把主页换了：
![2nd-360-security-contest-writeup_1](http://coffinblog.qiniudn.com/upload/2014/05/2nd-360-security-contest-writeup_1.png "2nd-360-security-contest-writeup_1")

下午我们就在尝试提权了，弄着弄着突然发现……web shell 上不了了！
肯定是密码被人改了！
我们马上上了 WordPress 后台，想通过主题编辑器来加一个 web shell ，对方实在狡猾，竟然把编辑器删了（想吐槽这尼玛算违规吧）……
我们利用了下插件编辑，发现插件是不能直接访问解析的，无果，愤怒，悲催，后悔……我们没改 web shell 默认密码:-)
然后就打算找到那个有漏洞的旧版本插件，再来一次，好不容易找到了，包含了下日志，日志文件太大了……访问不了。
在这个过程里，对手不停删我们的后台账户，他们还把主页改成 BUPT say .
士可忍孰不可忍！即使不为了分，我们也要把主页抢回来！
然后对手删账户，我们就远程连接 MySQL（还好有 web shell 的时候机智地把 MySQL 远程登陆开了）改密码……如此反复了十几次。
最终我们通过 MySQL 往 /tmp 里成功写了一句话，旧版本插件包含访问成功！（不知道为啥包含 /etc/passwd 不行）

这个时候快结束了……对手把后台都删了，主页都不能访问了……于是在最后我直接写成这样了：
![2nd-360-security-contest-writeup_2](http://coffinblog.qiniudn.com/upload/2014/05/2nd-360-security-contest-writeup_2.png "2nd-360-security-contest-writeup_2")
然后这一天就很欢快地过去了 :-)

今晚把《白帽子讲Web安全》中的某些章节再仔细看了看，联系这次比赛，又有了点新的收获。