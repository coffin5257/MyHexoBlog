title: Systemd 小记
tags:
  - systemd
id: 24
categories:
  - GNU/Linux
date: 2014-04-13 14:00:30
---

## Systemd 之前

以前我初接触 Linux 的时候，只知道/etc/init.d/nginx start之类的启动方式，加到开机启动的时候也只知道用 GUI（Ubuntu） ，最近好好了解了下这是什么机制。
<!--more-->

### System V Init：

1.  在这个过程里第一部执行的是 `/etc/rc.d/rc.sysinit` , 这个脚本的工作是初始化。2.  然后根据 `/etc/inittab` 这个文件中的内容，按照运行级别（runlevels）来执行。比如可能会有 `l5:5:wait:/etc/rc.d/rc5` 这意味着在运行级别 5（图形用户模式） 的时候，会执行 `/etc/rc.d/rc.5` 里面的配置文件，而其实这个目录下的东西是链接到别的地方的，可以用 `ls -l` 来查看一下，不同的发行版可能不一样。3.  执行 `/etc/rc.d/rc.local` 脚本文件
4.  执行 `/bin/login` 登陆程序

所以，你要给你自己装的某个程序做启动，那么就有两种思路：

*   大多数是这么干的：在 /etc/init.d/ 下有一个启动脚本，然后链接到别的地方，最终会链接到 /etc/rc.d/rc.*/ 下面
*   你还可以在 /etc/rc.d/rc.local 这里面添加命令

一旦想明白了，其实就这么简单对不对？

## chkconfig 命令

接着上面来讲：
chkconfig 是一个管理运行级别的命令，记住，它只是**管理**运行级别。
也就是说：他只会改变 `/etc/inittab` 的内容以及 `/etc/rc.d/rc.x/` 里的链接，并不会使某个服务状态突然发生改变。

chkconfig 用起来也很简单，比如：

```
chkconfig --list
chkconfig --list nginx
chkconfig --level 345 nginx off
```

了解了上面说的运行级别怎么一回事后，再看这个一目了然是不是？

## Systemd

在进入正题之前，还有一个启动方式不得不提，那就是 Upstart 。它的制作者是 Ubuntu 的人,不过现在（2014）大多数发行版都已经迁移到 Systemd 或者正在迁到 Systemd , Ubuntu 也在 Debian 决定迁往 Systemd（2014） 后宣布跟随上游迁往 Systemd .

好了我们进入正题吧，先看看维基百科上上怎么说的：

> systemd是Linux下的一种init软件，由Lennart Poettering带头开发，并在LGPL 2.1及其后续版本许可证下开源发布。其开发目标是提供更优秀的框架以表示系统服务间的依赖关系，并依此实现系统初始化时服务的并行启动，同时达到降低Shell的系统开销的效果，最终代替现在常用的SystemV与BSD风格init程序。

我们先抛开它采用了多么高大上的特性或者它到底怎么回事这回事儿……

然后……然后其实就没什么想说的了，更多的在各个发行版 Wiki 里都有提到。

我们就以 goagent 这个为例来说下怎么写吧：

### goagent启动方式

我们先来摸清 goagent 是怎么启动的，我是放在 /opt 下，在 goagent/local/ 里面有很多脚本啊，它其实给了一个传统的 SystemVinit 模式的脚本：proxy.sh .

在 goagent/local 目录下，可以执行这些命令：

```
./proxy.sh start
./proxysh stop
./proxy.sh restart
```

我们决定就用它了. 下面以我的 openSUSE 13.1 为例，其它发行版可能有些许区别，但这不是重点。

### systemd启动方式需要什么

我们通过各个 Wiki 以及 find 命令可以找到，在 /usr/lib/systemd/system 下有很多 *.service 和 *.target 文件 .

**是的，要做一个启动就得做一个 *.service 文件 .**

既然目标明确了，那么就开始吧，我们可以先看看 wiki 或者其它 service 文件，最终我写的是这样：

```
[Unit]
Description=GoAgent // service 描述
Wants=network.service 
#wants是这个意思:我需要你,但没你也行.
#如果我们这里换成 After=network.target(target是一种状态),那么当 network 没有的话，goagent 就不会启动.
[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/opt/goagent/local/proxy.sh
ExecStop=/opt/goagent/local/proxy.sh stop
ExecRestart=/opt/goagent/local/proxy.sh restart
[Install]
WantedBy=multi-user.target
#install 模块是指在systemd内部load一下
```

然后再执行：

`systemctl enable goagent.service`

就这些了，其它的……看需要去查吧。

## 参考

[维基百科](http://zh.wikipedia.org/zh-cn/Systemd)
[openSUSE:How to write a systemd service](https://zh.opensuse.org/index.php?title=openSUSE:How_to_write_a_systemd_service&amp;variant=zh-cn "openSUSE:How_to_write_a_systemd_service&amp;variant=zh-cn")