### 目前Nginx的upstream模块支持6种方式的负载均衡策略（算法）：轮询（默认方式）、weight（权重方式）、ip_hash（依据ip分配方式）、least_conn（最少连接方式）、fair（第三方提供的响应时间方式）、url_hash（第三方通过的依据URL分配方式）。

| 负载均衡策略 | 作用                                                         |
| ------------ | ------------------------------------------------------------ |
| 轮询         | 此策略适合服务器配置无状态且短平块的服务使用                 |
| 权重方式     | 此策略比较适合服务器硬件配置差距较大的情况。                 |
| ip_hash      | 此策略适合有状态服务的程序，比如session。                    |
| least_conn   | 此策略适合请求处理时间长短不一造成的服务器过载情况。         |
| fair         | 响应时间方式，按照服务器端的响应时间来分配请求，响应时间短的优先分配 |
| uri hash     | 可以使得同一个url（也就是同一个资源请求）会到达同一台服务器，一旦缓存住了资源，再次收到请求，就可以在缓存中读取 |

集群：多台服务器组成的响应大并发，高数据量访问的架构体系。

特点：1.成本高 2.能够降低单台服务器的压力，使用流量平均分配到多台服务器 3.使网站服务器架构更加安全稳定

apache:老牌的服务器软件，功能多并且稳定，支持多种配置lamp

nginx:安装简单小巧，并发量高，web服务器，代理服务器，邮箱服务器，lnmp，支持FastCGI，SSL，Virtual Host，URL Rewrite，Gzip等功能。并且支持很多第三方的模块扩展，Nginx的稳定性，功能集，示例配置文件和低系统资源的消耗让他后来居上，在全球活跃的网站中有12.18%的使用比例，大约为2200万个网站。

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
   #缓存图片的文件
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
   
   upstream [服务器组名称]{
   　　server [IP地址]:[端口号];
   　　server [IP地址]:[端口号];
   　　....
   }
   
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
   3. 让同一个用户访问同一个服务器-hash一致    +性，ip_hash一直刷新一直web2

6. session丢失会造成以下问题
   1. 用户状态无法判断用户是否登录
   2. 验证码没有办法验证 验证码生成值和校验的服务器不再一起



Nginx+PHP7的安装

```shell
教程地址：https://blog.csdn.net/weixin_33795806/article/details/93877637
```

1. nginx的编译源码需要依赖gcc环境，没有的话需要安装，yum install gcc-c++，zlib库提供了很多种压缩和解压缩的方式，nginx使用zlib对http包的内容进行gzip，所以需要安装zlib库，OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议，并提供丰富的应用程序供测试或其它目的使用。nginx不仅支持http协议，还支持https（即在ssl协议上传输http），所以需要在linux安装openssl库。

   ```shell
    yum -y install make zlib zlib-devel gcc-c++ libtool openssl openssl-devel
   ```

2. 首先需要安装PCRE

   PCRE(Perl Compatible Regular Expressions)是一个Perl库，包括 perl 兼容的正则表达式库。nginx的http模块使用pcre来解析正则表达式，所以需要在linux上安装pcre库，

3. 安装nginx

4. Nginx配置

   创建Nginx运行使用的用户和组www:

   ```
   /usr/sbin/groupadd www
   /usr/sbin/useradd -g www www
   ```

   配置nginx.conf，将/usr/local/webserver/nginx/conf/nginx.conf替换为以下的内容

   ```
   user www www;
   worker_processes 2;   # 设置值和系统CPU核心数一致
   error_log /usr/local/webserver/nginx/logs/nginx_error.log crit;  # 日志位置和日志级别
   pid /usr/local/webserver/nginx/nginx.pid;
   #Specifies the value for maximum file descriptors that can be opened by this process.
   worker_rlimit_nofile 65535;
   events
   {
   use epoll;
   worker_connections 65535;
   }
   http
   {
   include mime.types;
   default_type application/octet-stream;
   log_format main '$remote_addr - $remote_user [$time_local] "$request" '
   '$status $body_bytes_sent "$http_referer" '
   '"$http_user_agent" $http_x_forwarded_for';
   
   #charset gb2312;
   
   server_names_hash_bucket_size 128;
   client_header_buffer_size 32k;
   large_client_header_buffers 4 32k;
   client_max_body_size 8m;
   
   sendfile on;
   tcp_nopush on;
   keepalive_timeout 60;
   tcp_nodelay on;
   fastcgi_connect_timeout 300;
   fastcgi_send_timeout 300;
   fastcgi_read_timeout 300;
   fastcgi_buffer_size 64k;
   fastcgi_buffers 4 64k;
   fastcgi_busy_buffers_size 128k;
   fastcgi_temp_file_write_size 128k;
   gzip on;
   gzip_min_length 1k;
   gzip_buffers 4 16k;
   gzip_http_version 1.0;
   gzip_comp_level 2;
   gzip_types text/plain application/x-javascript text/css application/xml;
   gzip_vary on;
   
   #limit_zone crawler $binary_remote_addr 10m;
   # 下面是server虚拟主机的配置
   server
   {
   
   listen 8000;   # 监听端口（根据自己的需求更改）
   server_name localhost;   # 域名
   index index.html index.htm index.php;
   root /usr/local/webserver/nginx/html;   # 站点目录（也就是以后存放php文件的根目录）
   location ~ .*\.(php|php5)?$
   {
   #fastcgi_pass unix:/tmp/php-cgi.sock;
   fastcgi_pass 127.0.0.1:9000;   # 需要特别注意这个配置，这里是用来连接Nginx和PHP的php-fpm服务的默认端口9000，php-fpm服务后面会安装，可以根据自己的需求更改
   fastcgi_index index.php;
   include fastcgi.conf;
   }
   location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|ico)$
   {
   expires 30d;
   # access_log off;
   }
   location ~ .*\.(js|css)?$
   {
   expires 15d;
   # access_log off;
   }
   access_log off;
   }
   
   }
   ```

5. 检查nginx的配置文件是否语法正确

   ```shell
   /usr/local/webserver/nginx/sbin/nginx -t
   ```

6. 启动nginx

   ```
   /usr/local/webserver/nginx/sbin/nginx
   ps -ef | grep nginx
   ```

7. 访问nginx

   ```
   /usr/local/webserver/nginx/sbin/nginx -s reload      # 重新载入配置文件
   
   /usr/local/webserver/nginx/sbin/nginx -s reopen     # 重启nginx
   
   /usr/local/webserver/nginx/sbin/nginx -s stop         # 停止nginx
   ```

8. 安装php7

9. 安装php-fpm

   php-fpm能够让nginx和php连接起来
   
   ```shell
   yum install php70w-fpm php70w-opcache
   打开php-fpm配置文件
   
   vim /etc/php-fpm.d/www.conf
   
   修改以下内容（这里查找配置项时，可以在末行模式下使用斜杠加要查找的关键字回车查找，如下图所示）
   
   将listen.owner与listen.group参数分别修改为www
   ```

## 2. Nginx配置详解

文件结构解析地址：https://blog.csdn.net/saygood999/article/details/111191490

### 2.1	正向代理服务器和反向代理服务器的区别

> 1. 正向代理服务器主要有以下应用：
>    - 访问原来无法访问的资源
>    - 用作缓存，加快访问速度
>    - 对客户端访问授权，上网进行认证
>    - 代理可以记录用户访问举例（上网行为管理），对外隐藏用户信息
>
> 2. 反向代理主要有以下引用：
>
>    - 保护内网安全
>    - 负载均衡
>    - 缓存，减少服务器压力
>
> 3. 反向代理服务器和正向代理服务器的概念区别：
>
>    - 举个例子 A需要找C直接通过B（告诉B，我要访问C）-正向代理，A需要找C直接通过B（A只会认为访问B就能访问到C了，永远不知道C的存在）-反向代理
>
>    - 代理对象不同
>
>      正向代理的代理对象是客户端，服务端不知道实际发起请求的客户端。还是上面的举例，C君（服务端）不会知道A君（客户端）通过B君（代理）吃到了TA的巧克力，只会知道B君拿了TA的巧克力。简单理解就是，B君（代理）是站在A君（客户端）这边的。
>
>      反向代理的代理对象是服务端，客户端不知道实际提供服务的服务端。同样上面的举例，A君（客户端）不会知道巧克力是B君（代理）从C君（服务端）那里拿来给TA的，只会知道是B君给TA的。简单理解就是，B君（代理）是站在C君（服务端）这边的。
>
>    - 服务器架设位置不同
>
>      正向代理的服务器是架设在客户机和目标主机之间的。
>
>      反向代理的服务器是架设在服务器端的。
>
>      从代理对象的不同上可以很好理解为什么服务器架设位置不同。
>
>    - 用途不同
>
>      正向代理的主要用途，是为在防火墙内的局域网客户端提供访问Internet的途径。
>
>      反向代理的主要用途，是将防火墙后面的服务器提供给Internet访问。
>
>    - 总结
>
>      总的来说，正向代理是从客户端的角度出发，服务于特定用户（比如说一个局域网内的用户），以访问非特定的服务；反向代理则正好相反，是从服务端的角度出发，服务于非特定用户（通常是所有用户），以访问特定的服务。 

### 2.2	负载均衡

> 1. Nginx提供的负载均衡策略有2种：内置策略和扩展策略。内置策略仅为轮询，加权轮询，ip_hash，其余为扩展策略。
>
> 2. 配置
>
>    ```
>    upstream php61{
>    	#这里增加负载均衡的负载策略，以分号结束
>    	#分发的服务器，权重，分发最大失败次数，分发失败超时时间，可能宕机了
>    	server 192.168.73.130 weight=1 max_fails=3 fail_timeout=20s;
>    	server 192.168.73.131 weight=1 max_fails=3 fail_timeout=20s;
>    }
>    ```

### 2.3	web缓存

> 1. Nginx可以对不同的文件做不同的缓存处理，配置灵活，并且支持FastCGI_Cache，主要用于对FastCGI的动态程序进行缓存。配合着第三方的ngx_cache_purge，对制定的URL缓存内容可以的进行增删管理。
>
> 2. 配置
>
>    ```
>    #缓存图片的文件
>    location ~ \.(jpeg|jpg|png)${
>    	#缓存时间设置为1day
>    	expires 1d; 
>    }
>    ```

### 2.4	配置文件解析

> 
>
> ```shell
> ########### 每个指令必须有分号结束。#################
> #user administrator administrators;  #配置用户或者组，默认为nobody nobody。
> #worker_processes 2;  #允许生成的进程数，默认为1
> #pid /nginx/pid/nginx.pid;   #指定nginx进程运行文件存放地址
> error_log log/error.log debug;  #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
> events {
>     accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
>     multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off,该指令默认为off状态，意指每个worker process 一次只能接收一个新到达的网络连接
>     #use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
>     worker_connections  1024;    #最大连接数，默认为512
> }
> http {
>     include       mime.types;   #文件扩展名与文件类型映射表，也即前端请求的资源类型
>     default_type  application/octet-stream; #默认文件类型，默认为text/plain
>     #access_log off; #取消服务日志    
>     log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义格式
>     access_log log/access.log myFormat;  #combined为日志格式的默认值
>     sendfile on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
>     sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
>     keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。
> 
>     upstream mysvr {   
>       server 127.0.0.1:7878;
>       server 192.168.10.121:3333 backup;  #热备
>     }
>     error_page 404 https://www.baidu.com; #错误页
>     server {
>         keepalive_requests 120; #单连接请求上限次数。
>         listen       4545;   #监听端口
>         server_name  127.0.0.1;   #监听地址       
>         location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
>            #root path;  #根目录
>            #index vv.txt;  #设置默认页
>            proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
>            deny 127.0.0.1;  #拒绝的ip
>            allow 172.18.5.54; #允许的ip           
>         } 
>     }
> }
> 
> 1. 全局块
> 	该部分配置主要影响Nginx全局，通常包括下面几个部分：
> 
>   配置运行Nginx服务器用户（组）
>   worker process数
>   Nginx进程PID存放路径
>   错误日志的存放路径
>   配置文件的引入
> 2. events块
> 	该部分配置主要影响Nginx服务器与用户的网络连接，主要包括：
> 
>   设置网络连接的序列化
>   是否允许同时接收多个网络连接
>   事件驱动模型的选择
>   最大连接数的配置
> 3. http块
>   定义MIMI-Type
>   自定义服务日志
>   允许sendfile方式传输文件
>   连接超时时间
>   单连接请求数上限
> 4. server块
>   配置网络监听
>   基于名称的虚拟主机配置
>   基于IP的虚拟主机配置
> 5. location块
>   location配置
>   请求根目录配置
>   更改location的URI
>   网站默认首页配置
> ```

