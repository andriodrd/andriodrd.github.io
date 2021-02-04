# Nginx杂记

集群：多台服务器组成的响应大并发，高数据量访问的架构体系。

特点：1.成本高 2.能够降低单台服务器的压力，使用流量平均分配到多台服务器 3.使网站服务器架构更加安全稳定

apache:老牌的服务器软件，功能多并且稳定，支持多种配置lamp

nginx:安装简单小巧，并发量高，web服务器，代理服务器，邮箱服务器，lnmp

iis 微软公司的，给asp脚本使用可以通过fast-cgi的方式（网络基础服务） 使用Php

lighttpd 德国开发，小巧提供web服务支持

tengine 国内的nginx



nginx expires缓存功能

1. nginx缓存功能概述

   ```
   nginx通过配置，可以告知浏览器，返回数据的有效时间，浏览器就可以根据数据的有效时间，确定是否应该到服务器请求。如果数据没有超过有效期，就是用浏览器缓存的数据。
   缓存功能开启，是为了用户能够快速获取使用到数据，可以减少服务器请求，降低服务器压力
   ```

2. 开启

   ```
   #缓存图片你文件
   location ~ \.(jpeg|jpg|png)${
   	#缓存时间设置为1day
   	expires 1d;
   }
   ```

nginx的gzip压缩设置

1. nginx的gzip压缩功能概述

   ```shell
   压缩资源，通过网路发送的大小就更加节省资源了。带宽占用变小，启用压缩机制，为了能够快速访问到资源，web服务器进行压缩，浏览器需要进行解压缩操作，目前市场大部分浏览器是支持gzip解压缩的
   
   #配置gzip压缩
   gzip  on;
   gzip_min_length	1000;
   gzip_buffers		16 128k;
   #需要压缩的文件格式
   gzip_types		text/plain application/x-javascript text/css application/xml;
   #http的协议版本
   gzip_http_version 1.0;
   #如果是IE的话，就关闭压缩
   gzip_disable 'MSIE[1-6].';
   ```

负载均衡的配置

![image-20210204151104356](assets/image-20210204151104356.png)

负载均衡服务器（nginx） web1 web2，通过虚拟机克隆的方式快速搭建多个虚拟机，虚拟机必须处于关机状态，

1. 配置负载均衡服务器 

   ```
   #负载均衡服务器配置
   upstream php61{
   	#分发的服务器，权重，分发最大失败次数，分发失败超时时间，可能宕机了
   	server 192.168.73.130 weight=1 max_fails=3 fail_timeout=20s;
   	server 192.168.73.131 weight=1 max_fails=3 fail_timeout=20s;
   }
   ```

2. 分发的配置

   ```
   server{
   	listen 80;
   	server_name php61.php.com;
   	location / {
   		#分发代理
   		proxy_pass http://php61;
   	}
   }
   原理：访问域名找到虚拟主机，通过虚拟主机代理到upstream，upstream分发到各web服务器。
   ```

3. 启动各服务器

   windows域名解析到负载均衡服务器

   负载均衡服务器只需要启动nginx，不再进行php解析

   web服务器启动nginx和php解析

   ```
   /usr/local/php_nginx/sbin/php-fpm
   ps -A|grep php
   ```

4. 查看分发效果

   ```
   vim /usr/local/nginx/html/php61/index.html
   是单台服务器的并发量进行分发，一个人干的活分成多分人去干，但是成本也上去了
   ```

5. 问题来了-session丢失问题

   不同的服务器，需要session判断用户的状态，默认session是存储到服务器的硬盘文件中，不能共享

   1. 入库 Mysql memcache redis
   2. 硬盘共享方式 磁盘共享方式-不推荐
   3. 让同一个用户访问同一个服务器-hash一直性，ip_hash一直刷新一直web2

6. session丢失会造成以下问题
   1. 用户状态无法判断用户是否登录
   2. 验证码没有办法验证 验证码生成值和校验的服务器不再一起