title: Nginx 奇怪的 502
tags:
  - nginx
  - opensuse
categories:
  - GNU/Linux
date: 2014-07-23 19:35:01
---

今天在 VPS 上建站，用的 Nginx + php-fpm，配置好之后访问 静态 html 没问题，但是一旦访问 php 就 502 Bad Gateway ，很是忧伤！
调试了好久，排除了配置、内存、连接数限制等常见的可能导致 502 的原因，还是没有解决问题。
<!--more-->
在网上看到有些人说 50X 错误都是服务端(如php)的错误，与 nginx 无关，查看了 php-fpm 的日志也没发现什么问题，最终把
`fastcgi_pass unix:/var/run/php5-fpm.sock;`
换成
`fastcgi_pass 127.0.0.1:9000;`
竟然可以了！
事情当然不可能就这么完了，绕了好一会终于在 nginx 的 error_log 里发现了这个：

`[crit] 1458#0: *14 connect() to unix:/var/run/php5-fpm.sock failed (13: Permission denied) while connecting to upstream
`

###为什么会出现 Permission denied ？
看了下 /var/run/php5-fpm.sock 的权限：

`srw-rw---- 1 root root 0 Jul 23 07:56 php5-fpm.sock
`

果然！默认是由 root 创建的，而 nginx 被我配置成 `user nginx nginx;`

最终在 php-fpm.conf 里发现了：

```
;listen.owner = nobody
;listen.group = nobody
```

把它们改成：

`listen.owner = nginx
listen.group = nginx
`

看看在这个配置上面的说明：

> Set permissions for unix socket, if one is used. In Linux, read/write permissions must be set in order to allow connections from a web server. Many BSD-derived systems allow connections regardless of permissions.Default Values: user and group are set as the running user mode is set to 0660