title: openSUSE Nginx 启动脚本
tags:
  - nginx
  - 启动脚本
id: 21
categories:
  - GNU/Linux
date: 2014-03-29 14:00:52
---

防偷窥...
<!--more-->

## 脚本内容：

```
#! /bin/sh

# Description: Startup script for nginx on CentOS、SuSE、redhat. cp it in /etc/init.d and
# chkconfig --add nginx &amp;amp;&amp;amp; chkconfig nginx on
# then you can use server command control nginx
#
# chkconfig: 2345 08 99
# description: Starts, stops nginx

set -e

PATH=$PATH:/usr/local/nginx/sbin/
DESC="nginx daemon"
NAME=nginx
DAEMON=/usr/local/nginx/sbin/$NAME
CONFIGFILE=/usr/local/nginx/conf/nginx.conf
PIDFILE=/usr/local/nginx/logs/$NAME.pid
SCRIPTNAME=/etc/init.d/$NAME

# Gracefully exit if the package has been removed.
test -x $DAEMON || exit 0

d_start() {
    $DAEMON -c $CONFIGFILE || echo -n " already running"
}

d_stop() {
    kill -QUIT `cat $PIDFILE` || echo -n " not running"
}

d_reload() {
    kill -HUP `cat $PIDFILE` || echo -n " can't reload"
}

case "$1" in
    start)
        echo -n "Starting $DESC: $NAME"
        d_start
        echo "."
        ;;
    stop)
        echo -n "Stopping $DESC: $NAME"
        d_stop
        echo "."
        ;;
    reload)
        echo -n "Reloading $DESC configuration..."
        d_reload
        echo "reloaded."
        ;;
    restart)
        echo -n "Restarting $DESC: $NAME"
        d_stop
        sleep 1
        d_start
        echo "."
        ;;
    *)
    echo "Usage: $SCRIPTNAME {start|stop|restart|force-reload}" >&2
    exit 3
    ;;
esac

exit 0
```

## 使用方法：

```
vim /etc/init.d/nginx
chmod +x /etc/init.d/nginx
chkconfig nginx on
chkconfig --level 2345 nginx on
```

不出意外，你就可以使用
```
service nginx xxx
/etc/init.d/nginx xxx
```
这样的方式控制 nginx 了。

## Update：

[Systemd](http://www.coffin5257.com/systemd-notes) 文章写出来了，这里说明一下，这个脚本是传统的 SystemVInit 脚本，但是 chkconfig 后，service nginx start 这样的命令是可以用的，因为在 openSUSE 13.1 里，是兼容以前的启动方式的，通过 chkconfig 设置的实际上是调用 systemd .

Systemd 的目的就是打造一个各种发行版都能通用的启动程序，实际上就是这样，service 文件在各个发行版上是可以通用的.

既然有更快捷、更便于维护的启动方式，我们就应该积极去用它，推动它的发展，尤其是服务器级别的。

看看 Systemd 是怎么写的，简单多了是不是？

```
[Unit]
Description=The nginx HTTP and reverse proxy server
After=network.target remote-fs.target nss-lookup.target

[Service]
ExecStartPre=/usr/sbin/nginx -t
ExecStart=/usr/sbin/nginx -g "daemon off;"
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```