title: openSUSE 安装配置 Nginx
tags:
  - nginx
  - opensuse
id: 17
categories:
  - GNU/Linux
date: 2014-03-23 14:00:58
---

## 前言：为什么要写这个

这些内容在互联网上其实很容易找到，但是互联网上的信息实在太多了，对于新手——没有好的搜索技巧或者辨识信息的能力很难找到合适的东西。

我经常需要本地调试一些web程序，结果每次配 nginx 都去网上找，老是不能记住细节，希望借这篇日志能够梳理、总结一下。
<!--more-->

## Nginx 和 Apache

Apache 是很主流的 HTTP 服务器，我当年也是从WAMP（Windows 、Apache2 、MySQL 、Php5）开始的。但是 Nginx 作为新贵，近年来发展迅速。

与 Apache 相比，Nginx 有以下优势：

*   抗并发。在面对很多连接的时候，Nginx 比 Apache 占用资源要少很多。在这一点上，抗 DDos 很有优势。这一点我是有亲身体会的，学校的 Online Judge 系统当时为了举办一次大规模的线上比赛就重新部署了一次，把 Apache 换成 Nginx ;
*   高性能。在 Linux 上的 Nginx 配置文件里可以看到一个 epoll 开关，它具体是什么我们不需要管，只需要知道这个设计使得 Nginx 在 Linux 平台上进程很少（一个进程可以应付很多个外部连接，Apache 是一个连接开一个进程）。
*   热部署。Nginx 更改配置文件一般是不需要重启的，而 Apache 是需要的。
*   负载均衡和反向代理。这一点 Apache 应该也可以，我没有亲身经历。不过淘宝的前台是 Nginx （从《淘宝技术这十年》看到的）。

当然，存在即是道理，任何一款产品被设计之初，必然只是为了某些需求。所以 Nginx 也不是总赢 Apache ：

*   不擅处理动态文件。Nginx 很擅长处理静态页面。它不支持直接 php ，需要通过 FastCGI。
*   配置稍复杂。Apache 安装后可以直接用，但是 Nginx 装好后往往要经过一些配置才能满足需求。但是 Nginx 的配置方式是比 Apache 要容易的。
*   新贵的毛病。Nginx 仍在高速发展，所以可能会有一些难以预期的 bug ，不过  Nginx 服务器运行很稳。配置好了跑起来了就可以放那不用管了。还有支持的人少，网上一堆一堆的配置说明大同小异，很不靠谱。

还是要说一句：优劣都是相对的，选哪个取决于你的需求（比如个人网站没什么必要考虑高并发、负载均衡之类的）以及一些其它的因素（比如共享空间默认都是 Apache）。

## Tengine 和 Nginx

Tengine 是淘宝发起的 Web 服务器项目，它在 Nginx 的基础上，做了很多针对大访问量需求的优化。在淘宝网、天猫使用很广泛，现在是一个开源项目：[主页](http://tengine.taobao.org/)

本文用的是 Tengine ， 在配置上与 Nginx 并无不同。

## 安装

可以安装源里的：

`sudo zypper nginx`

我这里用的是 Tengine ，所以需要编译安装。

首先下载 Tengine ，解压后进入目录，只需要三行命令：

```
./configure
make
sudo make install
```

然后……你就会发现一堆 error 或者 not found……

编译安装的前提是需要这些东西：

`make  automake  gcc  gcc-c++  zlib  zlib-dev  openssl  openssl-dev  pcre  pcre-dev`

这些源里面基本都有，直接从源安装就好。后面几个在 openSUSE 里是：

`zlibrary  zlib-devel  openssl  libopenssl-devel  libpcre*  pcre-devel`

另外，`.configure` 有很多附加选项可以选，只要在 Tengine 目录里运行 `./configure –help` 就可以看到。这些选项可以让你自定义一些特性。

## 配置

我安装的时候没有加任何选项，所以安装完后整个都在 `/usr/local/nginx` 目录下，我们先来看看这个目录下有啥：

![nginx 目录内容](http://coffinblog.qiniudn.com/upload/2014/03/nginx_1.png)

说一下主要的：

*   logs 是日志文件夹；
*   sbin 里面是可执行文件，启动文件（nginx）就在里面；
*   conf 是配置文件夹；
*   html 就是默认的web文件夹，也就是 www 文件夹。

下面就开始基本的配置了，我们进入 conf 文件夹，里面一堆文件看得眼花缭乱，不过主要的配置文件就是 nginx.conf ，放心折腾，里面有个 nginx.conf.default 备份。

我们先看看默认的是什么样子（ #是注释）：

```
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    worker_connections  1024;
}

# load modules compiled as Dynamic Shared Object (DSO)
#
#dso {
#    load ngx_http_fastcgi_module.so;
#    load ngx_http_rewrite_module.so;
#}

http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

    # HTTPS server
    #
    #server {
    #    listen       443;
    #    server_name  localhost;

    #    ssl                  on;
    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_timeout  5m;

    #    ssl_protocols  SSLv2 SSLv3 TLSv1;
    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers   on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```

第一行：

    #user nobody;

我们把它去掉注释改成：

    user nginx nginx;


这是指定 nginx 进程的用户和组，如果是从源里安装的一般会默认添加 nginx 用户和组，编译安装的是没有的。

我们先来看看到底有没有( #表示 root 权限)：

查看组,有的话会显示出来：

    # cat /etc/group | grep nginx

添加组：

    # groupadd nginx

查看用户：

    # cat /etc/passwd | grep nginx

添加用户：

    # useradd -g nginx nginx

查看用户 nginx 信息：

    # id nginx

下一项：

    worker_processes 1;

我们把它改成机器 CPU 的核心数，看里面的 cpu cores ：

    # cat /proc/cpuinfo

log 方面用默认的就好，把第一个取消注释。再下面的 pid 也取消注释，做启动脚本的话，Systemd 会用到。

下面的 event ，我们在里面加上一行 `use epoll;` 前面说过这个模式很重要：

    events {
        worker_connections  1024;
        use epoll;
    }

这里的 worker_connections 是指一个进程处理多少连接，小站无所谓，大点的站可以开大一点。

然后可以看到一个用 **{ }** 围起来的大模块：**http**

把 gzip 打开。

然后把 gzip 后面的server部分全部注释掉，**注意最后一行的 } 不要注释掉了**。

在 gzip 后面加一行：

    include vhosts.d/*.conf;

这是 nginx 的为每一个 web 应用单独配置的机制，保证主配置文件的清洁。

我们在 conf 目录下再建一个 vhosts.d 目录，然后进入 vhosts.d ，这里开始为我们的 web 程序添加配置文件。

比如我要搭一个 WordPress ，新建一个 blog.coffin.conf :

配置：

    server { #建立一个服务器块

        server_name blog.coffin;
        listen      80;
        #listen     [::]:80; # 有 IPv6 的话

        location / { # 建立一个位置块
            root /home/coffin/public_html/blog.coffin/;
            index index.html; # 首页
        } # 位置块

        access_log  /var/log/blog.coffin.log;
    } #服务器块


这是原来的主配置文件里的 server 部分的精简，如果是一个纯 html 站点那么基本的就是这些了，但我们搭建的是 php 站点，所以需要配置处理 php 的部分：

    server { #建立一个服务器块

        server_name blog.coffin;
        listen      80;
        #listen     [::]:80; # 有 IPv6 的话

        location / { # 建立一个位置块
            root /home/coffin/public_html/blog.coffin/;
            index index.php; # 首页
        } # 位置块

        location ~ \.php$ { # 把 php 后缀都重定向给 php5-fpm 处理
            fastcgi_pass   unix:/var/run/php5-fpm.sock;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME /home/coffin/public_html/blog.coffin/$fastcgi_script_name;
            include        fastcgi_params;
        }
        access_log  /var/log/blog.coffin.log;

    } #服务器块

到这里基本完成配置了，可以平滑重启一下 nginx ：

    sudo /usr/local/nginx/sbin/nginx -s reload


我在这里遇到一次报错，提示配置文件多了一个 } ，可是反复找也没发现括号有什么问题，再一看提示错误那行的上一行，发现少了一个 ; 号。

## 安装配置 php5-fpm

要运行 WordPress 还没完，还需要安装 php 支持：

    sudo zypper in php5-fpm

装完之后要进行配置：

    sudo cp /etc/php5/fpm/php-fpm.conf.default php-fpm.conf

打开 php-fpm.conf ，调整配置如下：

    pid = /var/run/php5-fpm.pid
    error_log = /var/log/php5-fpm.log
    listen = /var/run/php5-fpm.sock
    pm.max_requests = 500

注意这些都不是在一起的，需要你去找。php-fpm.conf 里面是用 ; 符号作为注释，而且句尾不加 ; 符号

## 启动

要先注意你的机器上有没有占用了 80 端口的程序，查询下：

    netstat -anp | grep 80

启动 Nginx ：

    sudo /usr/local/nginx/sbin/nginx

启动 php5-fpm ：

    sudo systemctl enable php-fpm.service
sudo systemctl start php-fpm.service

启动 MySQL ：

    sudo systemctl enable mysql.service
    sudo systemctl start mysql.service

初始化 MySQL

openSUSE 默认安装的是 MariaDB , 启动 mysql.service 后，先设置下 root 的密码，这里我们设置成 root ：

    su
    mysqladmin -u root password root

登陆看看：

    mysql -uroot -p

输入密码后应该就可以登陆了。

我们新建一个数据库 coffinblog ：

    CREATE DATABASE `coffinblog` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;

然后我已为就可以安装了，我在浏览器输入 127.0.0.1 结果出现了一堆乱码：

> 鎮ㄧ殑PHP浼间箮娌℃湁瀹夎杩愯WordPress鎵€蹇呴渶鐨凪ySQL鎵╁睍銆�

我在目录下面写了一个 phpinfo() 文件，发现访问有效，说明 nginx+php 没问题。从这些乱码里面隐约可以看到 PHP MySQL  ，我估计是两者的连接出问题了，果然一检查发现 php5-mysql 没装。

安装之后果然 OK ，最后截图一张：

![成功安装wordpress截图](http://coffinblog.qiniudn.com/upload/2014/03/nginx_2.png)

安装的时候如果提示没有权限写入 wp-config.php ，可以给 blog.coffin 目录 777 权限：

    # chmod 777 -R /home/coffin/public_html
