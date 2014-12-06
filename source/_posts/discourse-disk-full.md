title: Discourse(Docker) 硬盘不够用
tags:
  - Discourse
  - Docker
id: 118
categories:
  - GNU/Linux
date: 2014-08-30 11:19:44
---

运营了一个 Discourse 论坛，使用的 Docker 容器，主页看这里：[www.discourse.org](http://www.discourse.org/)

这两天却突然发现一个问题，服务器硬盘不够用了，上去看了下，最终在官方论坛找到了解决方法，顺便写出来吧，中文玩这个的好像还不多。
<!--more-->

### Linux 下硬盘管理工具

`df`：`df -h`   #查看磁盘使用情况
`du`：`du -sch Dir_name`    #查看目录大小
`fdisk`：这是个分区工具

### 磁盘占用太大原因

使用上面的命令，最终你会定位到 `/var/lib/docker/aufs/diff` 这个目录。它几乎使用了 95% 的硬盘空间。

使用　`docker ps -a` 可以查看正在运行的 Docker 镜像以及以前用过的，和正常的 `ps -a` 是一个意思。那个 `STATUS` 显示为 `Up xxx hours` 的就是你正在用的，你可以把它的 Container ID 记下来。

使用 `docker images -a`，可以列出所有镜像。

可以发现，Discourse 每次 rebuild 后，原先的镜像都不会删除……可想而知，以 Discourse 的更新频率，会有多少旧的镜像。

### 解决方法

只要把无关的用 `docker rm` 删除就好了。
```
docker ps -a|grep Exit|cut -d' ' -f 1|xargs docker rm
docker images -a|grep '^&lt;none&gt;'|tr -s ' '|cut -d' ' -f 3|xargs docker rmi
```

参考：[https://meta.discourse.org/t/docker-diff-very-big-how-to-clean/12696](https://meta.discourse.org/t/docker-diff-very-big-how-to-clean/12696)