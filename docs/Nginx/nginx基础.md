# Nginx基础

## 1.	Nginx简介

> 1. Nginx是一款高性能的HTTP服务器和反向代理服务器，同时支持IMAP/POP3/SMTP代理服务，官方测试nginx能够支撑5万并发链接，并且cpu，内存等资源消耗却非常低，运行非常稳定。

## 2.	Nginx安装

```
Nginx+php7教程安装地址：https://blog.csdn.net/weixin_33795806/article/details/93877637
```

> 1. 首先创建文件制作自动安装脚本
>
>    ```shell
>    #创建openresty.sh安装脚本文件(openresty版的nginx)
>    vim openresty.sh
>    
>    
>    yum install -y pcre-devel openssl-devel gcc curl
>    cd /usr/local
>    wget https://openresty.org/download/openrestr-1.17.8.2.tar.gz
>    cd /usr/local
>    tar -zxvf openresty-1.17.8.2.tar.gz
>    cd /usr/local
>    mv openresty-1.17.8.2 openresty
>    cd /usr/local/openresty
>    ./configure --with-luajit --without-http_redis2_module --with-http_icon_module
>    cd /usr/local/openresty/
>    make && make install
>    
>    
>    #赋予执行文件
>    chmod +x openresty.sh
>    运行即可
>    ./openresty.sh
>    ```

## 3.	Nginx配置文件

```shell
配置文件的几大块
nginx配置文件主要分为六个区域，核心区域
main{全局设置}作用域就是全局
events（nginx工作模式）
http(http设置)
	upstream(负载均衡服务器设置)
		server(主机设置)
			location(url匹配)
```

> ```shell
> ########### 每个指令必须有分号结束。#################
> #user administrator administrators;  #配置用户或者组，默认为nobody nobody。
> #worker_processes 2;  #允许生成的进程数，默认为1,一般是核心数的2倍
> #pid /nginx/pid/nginx.pid;   #指定nginx进程运行文件存放地址
> error_log log/error.log debug;  #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
> events {
>  accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
>  multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off,该指令默认为off状态，意指每个worker process 一次只能接收一个新到达的网络连接
>  #use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
>  worker_connections  1024;    #最大连接数，默认为512
> }
> http {
>  include       mime.types;   #文件扩展名与文件类型映射表，也即前端请求的资源类型
>  default_type  application/octet-stream; #默认文件类型，默认为text/plain，设定了默认的类型为二进制流，也就是当文件类型未定义时使用这种方式，例如在没有配置asp的location环境时，Nginx是不予解析的，此时，用浏览器访问asp文件就会出现下载了。
>  #access_log off; #取消服务日志    
>  log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义日志格式，用于设置日志的格式，和记录哪些参数，这里设置为main刚好用于access log来记录这种类型。
>  access_log log/access.log myFormat;  #combined为日志格式的默认值
>  sendfile on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
>  sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
>  keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。
> 
> #gzip on;
> 
>  upstream mysvr {   
>    server 127.0.0.1:7878;
>    server 192.168.10.121:3333 backup;  #热备
>  }
>  error_page 404 https://www.baidu.com; #错误页，即404归到百度页面。
>  server {
>      keepalive_requests 120; #单连接请求上限次数。
>      listen       4545;   #监听端口
>      server_name  127.0.0.1;   #监听地址，虚拟主机地址     
>      location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
>         #root path;  #根目录
>         #index vv.txt;  #设置默认页
>         proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
>         deny 127.0.0.1;  #拒绝的ip
>         allow 172.18.5.54; #允许的ip           
>      } 
>  }
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

## 4.	Nginx反向代理

```
应用场景：分担服务器的压力，不同服务指向不同服务的代理器。
```

```shell
#反向代理案例配置
upstream tuling{
	#设置我们的服务组群
	server 127.0.0.1:8080 max_fails=3 fail_timeout=30s weight=5;
	server 127.0.0.1:8081 max_fails=3 fail_timeout=30s weight=5;
}
upstream tuling1{
	#设置我们的服务组群
	server 127.0.0.1:8082 max_fails=3 fail_timeout=30s weight=5;
	server 127.0.0.1:8083 max_fails=3 fail_timeout=30s weight=5;
}
server{
	listen 80;
	server_name 127.0.0.1;
	location /{
	#通过location的proxy_pass转发到我们的服务组群
		proxy_pass http://tuling;
		index index.html index.htm index.jsp;#优先去找tomcat的首页，没找到到的话再去找nginx的首页
	}
	location /test.html{
	#通过location的proxy_pass转发到我们的服务组群
		proxy_pass http://tuling1;
		index index.html index.htm index.jsp;#优先去找tomcat的首页，没找到到的话再去找nginx的首页
	}
}
```

## 5.	负载均衡

| 负载均衡策略 | 作用                                                         |
| ------------ | ------------------------------------------------------------ |
| 轮询         | 此策略适合服务器配置无状态且短平块的服务使用                 |
| 权重方式     | 此策略比较适合服务器硬件配置差距较大的情况。                 |
| ip_hash      | 此策略适合有状态服务的程序，比如session。                    |
| least_conn   | 此策略适合请求处理时间长短不一造成的服务器过载情况。         |
| fair         | 响应时间方式，按照服务器端的响应时间来分配请求，响应时间短的优先分配 |
| uri hash     | 可以使得同一个url（也就是同一个资源请求）会到达同一台服务器，一旦缓存住了资源，再次收到请求，就可以在缓存中读取 |

```shell
upstream php61{
	#这里增加负载均衡的负载策略，以分号结束
	#分发的服务器，权重，分发最大失败次数，分发失败超时时间，可能宕机了
	server 192.168.73.130 weight=1 max_fails=3 fail_timeout=20s;
	server 192.168.73.131 weight=1 max_fails=3 fail_timeout=20s;
}
server{
	listen 80;
	server_name 127.0.0.1;
	location /{
	#通过location的proxy_pass转发到我们的服务组群
		proxy_pass http://php61;
		index index.html index.htm index.jsp;#优先去找tomcat的首页，没找到到的话再去找nginx的首页
	}
}
```

## 6.	Nginx限流

```
限流：限制单位时间内用户访问服务器的次数,场景：秒杀，或者高并发，超出服务器的承受的体量

Nginx限流主要分为两种方式：
限制访问频率
限制并发连接数

根据实时传输（处理）速率，对突发流量不做额外处理，而令牌桶算法能够在限制数据的平均传输速率的同事允许某种程度的突发传输
Nginx按请求速率限速模块使用的是漏桶算法，即能够强行保证请求的实时处理速度不会超过设置的阀值
```

| 限流算法名称 | 原理                                                         | 优缺点                                                       |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 漏桶         | 控制单位时间的流速，当流入的速度大于流出的速度并且超出桶容量时溢出 | 面对突发的超高流量，对于超出的流量只能拒绝                   |
| 令牌桶       | 控制1秒产生的令牌数量，匀速的放入令牌桶中，请求访问进来，先尝试获取令牌，获取到令牌则正常通过，反之限制 | 针对突发的大流量可以预消费，像是信用卡一样，先透支，对于后面的请求，则需要还清欠款（透支掉的令牌），延迟一段时间还清欠款后继续执行 |
| 计数器       | 定义                                                         |                                                              |
| 滑动窗口     | 定义                                                         |                                                              |

> 1. HttpLimit zone
>
>    ```shell
>    指令：
>    limit_req_zone:
>    limit_conn_zone:
>    ab测试安装:
>    ```
>
>    ```shell
>    #本模块可以针对条件，进行会话的并发连接数控制（例如：限制每个IP的并发连接数）
>    #配置示例
>    http{
>    	limit_zone one $binary_remote_addr 10m;
>    	...
>    	server{
>    	...
>    	location /download/{
>    		limit_conn one 1;
>    	}
>    	}
>    }
>    ```
>
>    ```shell
>    #Nginx限流的两种法师其一限制访问速率：
>    
>    #备注：Nginx中使用nginx_http_limit_req_module模块来限制的访问频率，限制的原理实质是基于漏桶算法原理来实现的。在nginx.conf配置文件中可以使用limit_req_zone命令及limit_req命令限制单个IP的请求处理频率。
>    
>    #正常流量限制访问频率语法结构：
>    #语法结构：
>    #limit_req_zone key zone rate
>    #对于上面语法结构的参数简单做下解释：
>    #key: 定义需要限流的对象。
>    #zone: 定义共享内存区来存储访问信息。
>    #rate: 用于设置最大访问速率。
>    
>    #简单例子：
>    http {
>    	limit_req_zone $binary_remote_addr zone=myLimit:10m rate=3r/s;
>    }
>    
>    server {
>      location / {
>        limit_req zone=myLimit;
>        rewrite / http://www.niyueling.cn permanent;
>      }
>    }
>    
>    #对配置简单做下解释：
>    #上面binary_remote_addr就是key，表示基于客户端ip(remote_addr)进行限流，binary_表示压缩内存占用量。定义了一个大小为10M，名称为myLimit的内存区，用于存储IP地址访问信息。rate设置IP访问频率，rate=5r/s表示每秒只能处理每个IP地址的5个请求。Nginx限流是按照毫秒级为单位的，也就是说1秒处理5个请求会变成每200ms只处理一个请求。如果200ms内已经处理完1个请求，但是还是有有新的请求到达，这时候Nginx就会拒绝处理该请求。
>    
>    
>    #突发流量限制访问频率
>    #上面的配置一定程度可以限制访问频率，但是也存在着一个问题：如果突发流量超出请求被拒绝处理，无法处理活动时候的突发流量，这时候应该如何进一步处理呢？Nginx提供burst参数结合nodelay参数可以解决流量突发的问题，可以设置能处理的超过设置的请求数外能额外处理的请求数。我们可以将之前的例子添加burst参数以及nodelay参数：
>    
>    http {
>    limit_req_zone $binary_remote_addr zone=myLimit:10m rate=3r/s;
>    }
>    
>    server {
>    location / {
>    limit_req zone=myLimit burst=5 nodelay;
>    #设置失败的请求返回的状态码：598而不是404了
>    limit_req_status 598;
>    rewrite / http://www.niyueling.cn permanent;
>    }
>    }
>    
>    #可以看到我在原有的location中的limit_req指令中添加了burst=5 nodelay，如果没有添加nodelay参数，则可以理解为预先在内存中占用了5个请求的位置，如果有5个突发请求就会按照200ms去依次处理请求，也就是1s内把5个请求全部处理完毕。如果1s内有新的请求到达也不会立即进行处理，因为紧急程度更低。这样实际上就会将额外的5个突发请求以200ms/个去依次处理，保证了处理速率的稳定，所以在处理突发流量的时候也一样可以正常处理。如果添加了nodelay参数则表示要立即处理这5个突发请求。
>    
>    #Nginx限流的两种法师其一限制并发连接数：
>    
>    #Nginx中的ngx_http_limit_conn_module模块提供了限制并发连接数的功能，可以使用limit_conn_zone指令以及limit_conn执行进行配置。接下来我们可以通过一个简单的例子来看下：
>    
>    http {
>    limit_conn_zone $binary_remote_addr zone=myip:10m;
>    limit_conn_zone $server_name zone=myServerName:10m;
>    }
>    
>    server {
>    location / {
>    limit_conn myip 10;
>    limit_conn myServerName 100;
>    rewrite / http://www.niyueling.cn permanent;
>    }
>    }
>    
>    #上面配置了单个IP同时并发连接数最多只能10个连接，并且设置了整个虚拟服务器同时最大并发数最多只能100个链接。当然，只有当请求的header被服务器处理后，虚拟服务器的连接数才会计数。刚才有提到过Nginx是基于漏桶算法原理实现的，实际上限流一般都是基于漏桶算法和令牌桶算法实现的。接下来我们来看看两个算法的介绍：
>    
>    
>    #漏桶算法与令牌桶算法的区别
>    #两种算法都能够限制数据传输速率，但令牌桶还允许某种程度的突发传输。因为令牌桶算法只要令牌桶中存在令牌，那么就可以突发的传输对应的数据到目的地，所以更适合流量突发的情形下进行使用。
>    ```

## 7.	Nginx动静分离

```shell
Nginx的静态处理能力很强，但是动态处理能力不足，因此，在企业中常用动静分离技术，动静分离技术其实是采用代理的方式，在server{}段中加入带正则匹配的location来指定匹配项针对服务器的动静分离
实现整个网站的动静分离，实现如下要求：
1. 前端nginx收到静态请求，直接从NFS中返回给客户端
2. 前端nginx收到动态请求转交给FastCGI交给服务器处理
	--如果得到静态结果直接从NFS取出结果交给nginx然后返回给客户端
	--如果需要数据处理服务器连接数据库后将结果返回给nginx
3. 前端nginx收到图片请求以.jpg .png .gif等请求交给后端images服务器处理
#示例代码
location ~* \.(gif|ipg)${ # location匹配将图片交给image处理
	proxy_pass http://10.10.0.23:80;# image服务器需要开启web服务
}
```

> 1. 案例
>
>    ```
>    upstream tuling{
>    	server localhost:8080;
>    }
>    upstream tuling1{
>    	server localhost:8081;
>    }
>    server{
>    	listen 80;
>    	server_name localhost;
>    	location /test.html{
>    		proxy_pass http://tuling;
>    	}
>    	location ~*\.)(jpg|gif)${
>    		proxy_pass http://tuling1;
>    	}
>    }
>    ```

## 8.	Nginx镜像服务器

> 1. Nginx的proxy_store作用是直接把静态文件在本地磁盘创建并读取，类似于七牛或者又拍这样的镜像CDN功能，首次访问会自动获取源站的静态图片等文件，之后的访问就是直接从CDN服务器读取，加快了速度。

```shell
#需要配置一下参数：
#启动缓存到本地的功能：
proxy_store on;
#表示用户读写权限，如果error中报路径不允许访问的话就用chmod -R a+rw将下面配置的路径改为相应的权限
proxy_store_access user:rw  group:rw all：rw;
#此处为文件的缓存路径，这个路径是和url中的文件路径一致的
proxy_temp_path 缓存目录；
#在上面的配置之后，虽然文件被缓存到了本地磁盘上，但每次请求仍会向远端拉取文件，为了避免去远端拉取文件，还必须增加：
if (!-e $request_filename){
	proxy_pass http://192.168.10.10;
}
#注：'!-e $request_filename'正则表达式，匹配缓存目录中的文件与源文件是否存在。
#'http://192.168.10.10'源服务器的地址，默认端口80，如监听其他端口，此处要指出，例如4000端口，http://192.168.10.10：4000
```



```java
#整体配置如下（修改nginx的配置文件nginx.conf）:
location / {
  #这里的location是要换成自己经过精确匹配的location，比如要缓存图片要写成location ~*\.(gif|ipg|jepg|png|bmp)${'
    expires 3d; //所有连接，浏览器缓存过期时间为3天
    proxy_set_header Accept-Enconding';
    root /home/mpeg/nginx; //此目录为服务器的根目录，下面的if语句就是判断此目录下是否有响应的文件,和Url中的文件路径一致的
    proxy_store on;   //表示开启缓存
    proxy_store_access user:rw group:rw all:rw;//表示用户读写权限 
    proxy_temp_path /home/mpeg/nginx;        //此处为文件的缓存路径，这个路径是和url中的文件路径一致的 
    if ( !-e $request_filename)  {
                proxy_pass http://192.168.0.1;   //此处为要被代理的服务器的地址
       }                                                                            
  }
}
```



```java
#实例1
location / {
            expires 3d;
            proxy_set_header Accept-Encoding '';
            root /data/wwwroot/img.freehao123.com;
            proxy_store on;
            proxy_store_access user:rw group:rw all:rw;
            proxy_temp_path /data/wwwroot/img.freehao123.com/temp;
            if ( !-e $request_filename)  {
                 proxy_pass https://www.freehao123.com;
            }
  }

#和实例1想用效果的代码
upstream http_tornado {
    server www.freehao123.com:443;
}
 
server {
    # 省略其他配置
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|js|html|htm|css)$ {
        root /opt/data/product/blog/cache;
        proxy_store on;
        proxy_store_access user:rw group:rw all:rw;
        proxy_temp_path /opt/data/product/blog/cache;
        if ( !-e $request_filename) {
            proxy_pass  http://http_tornado;
        }
    }
} 

// 参考文档：https://www.freehao123.com/nginx-cdn/
```

## 9.	Nginx的热备部署

````java
//1. 安装keepalived
yum nstall nginx keepalived pcre-devel -y
vim keepalived.conf

global_defs{
	vrrp_garp_interval 0
  vrrp_gna_interval 0
}
vrrp_instance VI_1{
  state MASTER //备用机 修改为BACKUP,一个主机多个备份
  interface enpos8
  virtual_router_id 50
  priority 100 #参数 备用比主机低就行了
  advert_int 1
  authentication {
    auth_type PASS
    auth_pass 1111
  }
 	virtual_ipaddress{ //这里需要配置，虚拟ip
    192.168.56.120
}
  
  
 //nginx的服务热备，只要加一个backup即可
 upstream http_tornado {
    server www.freehao123.com:443 backup;
}
````

