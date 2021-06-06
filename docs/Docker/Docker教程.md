# Docker教程

## 1.	docker介绍

### 1.1	什么是容器
```shell
1. docker也是容器，装应用的，就好像是笔筒也是容器，装笔的。
```

### 1.2	什么是docker

```shell
1. docker 是一个开源的应用容器引擎技术，容器是完全使用沙箱（进程不会相互影响）机制，一次封装，处处迁移。
2. 一种轻量化的虚拟化技术，类似于虚拟机，又不同于虚拟机
```




### 1.3	为什么使用docker
```shell
1.	程序员
	1. 搭建开发环境
2.	测试人员
	1. 搭建测试环境
3.	运维人员
	1. 搭建运维环境
```
### 1.4	docker vs wmware(or virtualbox)

```shell
Docker不同于虚拟机：
  1. 首先虚拟机对宿主机的资源消耗很大（由于虚拟机需要独立的cpu 内存 磁盘 网络和完整的操作系统）--如果只是运行一两台虚拟机还好，多了宿主机就承受不住了---如上线环境服务器集群  而docker迁移方便，占用资源少，不会虚拟出硬件资源，共用宿主机硬件资源
  2. 启动时间更少，仅仅保留了系统运行时所需要的最低运行配置
  3. 区别->特性：虚拟机：容器
  	1、隔离级别：内核级（操作系统）：进程级
  	2、隔离策略：Hypervisor:CGroups
  	3、系统资源：5~15%：0~5%
  	4、启动时间：分钟级；秒级
  	5、镜像存储：GB~TB:KB~MB
  	6、集群规模：上百：上万
  	7、高可用策略：备份，容灾，迁移：弹性，负载，动态
  	
```

### 1.5	docker架构
```shell
1.	docker是cs架构+docker hub:其中:docker 调用的是restful接口，所以docker inspect 镜像名返回的是一个json形式的内容
2.	client客户端可以进行如下操作：
	1、操作container,image,network,data volumes
3.	比喻
	1、Docker：汽车引擎
	2、Dockerfile：汽车图纸
	3、Images：类似于样板车
	4、Container：把一些汽车的零件，拼接在一起
	5、Docker Compose：老司机，可以开多辆车，容器编排
	6、Docker Volume：汽车的邮箱，做数据持久化
	7、Docker Swarm：交通枢纽，管理
	8、Docker Registry：4s店
```

## 2.	安装docker

### 2.1	依赖

```shell
1、kernal version >= 3.8
	2、uname -a | awk '{split($3,arr,"-");print arr[1]}'
		1. 利用split切分字符串并放到数组中
		2. split有3个参数，第一个传要切分的字符串，第二个放切分完后输出的数组，第三个定义分隔符
	3、[ $(uname -a | awk '{split($3,arr,"-");print arr[1]}') > '3.8' ] && echo -e "Linux version > 3.8\nCan install Docker." || echo -e "Linux version < 3.8\nCan't install Docker."   ->用于判断内核是否支持装docker
```

### 2.2	在Windows中安装docker

```shell
官方安装教程：https://docs.docker.com/docker-for-windwos/
```

### 2.3	在linux中安装docker

```shell
1.二话不说先卸载旧版本
			sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine   
	2.安装依赖包
			sudo yum install -y yum-utils \
  			  device-mapper-persistent-data \
               		lvm2
	3.设置阿里云源地址仓库
			 sudo yum-config-manager \
              	 --add-repo \
      		http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
	4.安装docker
		Centos6.5 sudo yum -y install docker-io

		Centos7.2 sudo yum install docker-ce docker-ce-cli containerd.io
		报错参考下面网址：https://blog.csdn.net/jiaminbao/article/details/98954342
	5.启动docker
		systemctl start docker
	6.查看安装版本
		docekr version
	7.配置不需要加sudo进行操作docker的命令
		sudo usermod -aG docker $LOGNAME
```

### 2.4	在虚拟机中安装docker

### 2.5	在线练习docker

```shell
katacoda网站：https://www.katacoda.com/
```

### 2.6	测试是否安装成功

```shell
docekr version
```

## 3.	基本操作

### 3.1	初探
```shell
1. docker --help
  Management Commands:(后续不会被淘汰的版本命令)
    builder     Manage builds
    config      Manage Docker configs
    container   Manage containers
    context     Manage contexts
    engine      Manage the docker engine
    image       Manage images
    network     Manage networks
    node        Manage Swarm nodes
    plugin      Manage plugins
    secret      Manage Docker secrets
    service     Manage services
    stack       Manage Docker stacks
    swarm       Manage Swarm
    system      Manage Docker
    trust       Manage trust on Docker images
    volume      Manage volumes
2. docker image --help(进一步查询docker image的帮助命令)
    Management Commands:
      builder     Manage builds
      config      Manage Docker configs
      container   Manage containers
      context     Manage contexts
      engine      Manage the docker engine
      image       Manage images
      network     Manage networks
      node        Manage Swarm nodes
      plugin      Manage plugins
      secret      Manage Docker secrets
      service     Manage services
      stack       Manage Docker stacks
      swarm       Manage Swarm
      system      Manage Docker
      trust       Manage trust on Docker images
      volume      Manage volumes
3.	再去进一步操作命令
```
### 3.2	docker image(增删改查)
```shell
1. 列出镜像
	1、列出所有镜像
		docker images
	2、仅列出镜像的ID
		docker iamges -q
2. 下载镜像
	1、docker pull nginx
3. 删除镜像
	1、docker rmi ImgesID
4. 查看镜像详细信息
	1、docker inspect jenkins
```
### 3.3	docker container
```shell
1. 运行容器
	1、docker run image
	2、docker run -it centos /bin/bash
		cat /etc/re
		cat /etc/redhat-release
2. 列出容器
	1、列出当前运行的容器
	docker ps
	2、列出系统中所有的容器
	docker ps -a
	3、列出容器上允许的所有历史命令
	sudo docker history centos
3. 查看容器的进程和资源利用情况
	1、查看容器的进程
	docker top ContainerID
	2、查看容器的资源利用情况
	docker stats ContainerID
4. 停止/开启容器
	1、停止容器
	docker stop ContainerID
	2、开启容器
	docker restart ContainerID
	3、暂停/启动容器
	docker pause web
	docker unpause web
5. 删除容器
	1、删除一个未运行的容器
		docker rm web
	2、删除一个正在运行的容器
		docker rm -f web
4. docker run 出来一个容器，可能是直接运行失败的，可能需要进入容器里面启动一些服务才可以运行
5. 例子
	1、docker container run --help | sed -nr '/\-d,|\-i,|\-t,|\-p,|\-P,/p'\
	2、docker run --name web nginx -d
	3、docker container rm -f web //强制删除，不管容器是否停止或运行
	4、docker run --name web -d -p 9000:80 nginx //大写的P随机给你指定一个端口，小写的p则自己指定端口的映射
	5、docker run --name flask_app -d -p 9888:5000 flask_app # 这个5000端口是dockerfile文件里EXPOSE暴露的端口
	6、docker logs flask_app # 查看容器的日志
```

### 3.4	共享images

```shell
1. 上传image到docker repository
	1、登录
		docker login
	2、推送
		docker build -t keyou1/flask_app:latest
		docekr push keyou1/flask_app:latest
2. 导出为压缩文件，再导入
	1、导出为压缩文件
	docker save -o apitest.tar apitest:latest
	2、导入压缩文件
	docker load -i apitest.tar
```



## 4.	network

```shell
1. ip -a中的docker0可以看做是一个路由器，其中的docker镜像起来的容器的ip地址通过docker0这个路由器网卡进行网络的访问。可以用docekr inspect 
```

### 3.1	docker0

```shell
默认容器与宿主机之间沟通的桥梁
```

### 3.2	网络类型

```shell
1. bridge
	1、nat网络模型
	2、虚拟交换机
2. host
	1、与宿主机共享网络
	2、--net=host
3. none
	1、不配置网络
		--net=none
4. overlay
	1、不同网络进行通信
与一个容器共享网络，--net=container:ContainerName
```

### 3.3	相关操作

```shell
1. 查看
	1、docker network ls
	2、docker network inspect networkname
2. 创建
	1、docker network create --driver drivername name
	2、docker network create -d bridge --subnet 172.16.100.0/24 one_network
```

### 3.4	案例演练

## 5.	volume

### 5.1	介绍

```shell
1. 使用数据卷实现数据持久化
2. 数据备份/数据共享
```



### 5.2	相关操作

```shell
1. 创建
	docker volume create [options] [volume]
2. 查看
	docker volume inspect [options] [volume...]
3. 挂载
	docker run -v 宿主机目录：容器目录
4. 删除
	docker volume rm [options] [volume...]
```

## 6.	Dockerfile

```dockerfile
1.	 docker build -t alpine_1.0 -f ./Dockerfile2 .
	备注：如果命令是Dockfile的话，使用.就行，如果不是Dockerfile的话，那么需要使用-f来指定Dockerfile文件的路径；最后这个"."是不管怎样都需要加上去的。
```

### 6.1	初探

```dockerfile
FROM python:3-alpine3.9
LABEL maintainer='15270265094@163.com'
LABEL description='Simple deploying flask project'

# cd /usr/src/app
WORKDIR /usr/src/app

# COPY ./dev01 ./
ADD ./dev01.tar.gz ./

WORKDIR ./dev01

# 一个RUN指令，就在上一层的基础上，新增一层
RUN pip install -i https://pypi.douban.com/simple -r requirements.txt

VOLUME /usr/src/app/dev01
EXPOSE 5000
CMD [ 'python', 'run.py' ]
```

### 6.2	FROM

```dockerfile
1.	指明base image(在哪一个image的基础上创建)
```

### 6.3	LABEL

```dockerfile
1.	指明Dockerfile的作者
2.	不执行的指令
```

### 6.4	RUN

```dockerfile
1.	在已存在的image顶层执行命令，创建一个新的layer层
2.	往往将需要安装的依赖，使用一个RUN来创建
	RUN apt-get update && apt-get install -y nginx 
```

### 6.5	ENV

```dockerfile
1.	在容器内创建环境变量
ENV username=keyou password=123456
```

### 6.6	COPY

```dockerfile
1.	复制文件\目录到容器中
```

### 6.7	ADD

```dockerfile
1.	跟COPY类似
2.	有额外特性
	1、能解压压缩文件，然后复制到容器中
	2、能远程下载
	ADD http://www.keyou.site/keyou.tar.xz /usr/src
```

### 6.8	WORKDIR

```dockerfile
1.	切换目录
```

### 6.9	USER

```dockerfile
1.	设置运行其他命令的用户（UID）
	USER root
```

### 6.10	VOLUME

```dockerfile
1.	指定需要持久保存的目录（数据备份）
VOLUME /data
```



### 6.11	ENTRYPOINT vs	CMD

```dockerfile
1.	CMD
	1、运行容器时，不加任何参数，会自动执行CMD,如果添加参数则会忽略CMD
	2、如果有多个CMD,则只有最后一个会执行，其他都会被忽略
	3、可以给ENTRYPOINT传参（CMD ["param1","param2"]），如果ENTRYPOINT使用shell format,CMD会被忽略
2.	ENTRYPOINT
	1、运行容器时，如果添加参数不会忽略ENTRYPOINT
3.	相同点
	1、只有1个CMD或者ENTRYPOINT时，两者无区别
	2、shell格式和exec格式
		1.shell格式
        ENTRYPOINT ls /
		2.exec格式
		ENTRYPOINT [ "/bin/cat" ]
		CMD[ "/etc/passwd" ]
```



## 7	Docker Compose

```dockerfile
1.	会自动创建镜像
	1、docker_compose_容器名_序号
2.	使用命令
	1、docker-compose -f docker_compose.yml ps # 会列出这个docker-compose.yml文件启动的容器的相关信息。
3.	介绍
	1、Docker Compose来轻松高效的管理容器，定义运行多个容器。
4.	三步骤
	1、Dockerfile保证我们的项目在任何地方可以运行
	2、需要一个docker-compose.yml文件
	3、启动项目：docker-compose up -f docker-compose.yml -d
5.	yaml规则
	# 3层
	version: '' # 版本
	services: # 服务
	  服务1：web
	    # 服务配置
	    images
	    build
	    network
	    deploy:
	      replicas:6 # 这是副本
	    ...
	  服务2：redies
	    # 服务配
	    ...
	  服务3：redies
	# 其他配置 网络/卷、全局规则
	volumes:
	networks:
	configs:
```



### 7.1	安装

```dockerfile
安装网址：https://docs.docker.com/compose/install/
1.	# install docker compose
	sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
	# 下面这个地址可能快点！
	curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.5/docker-compose-'uname -s'-'uname -m' > /usr/local/bin/docker-compose
2.	# Apply executable permissions to the binary给下载的这个文件授权
	sudo chmod +x /usr/local/bin/docker-compose
3.	# 测试安装是否成功
    $ docker-compose --version
    docker-compose version 1.29.2, build 1110ad01
4.	# 体验地址
	https://docs.docker.com/compose/gettingstarted/
	python应用。计数器，redis!
	1、应用app.py
	2、Dockerfile应用打包为镜像
	3、Docker-compose.yml文件（定义整个服务，需要的环境，web,redis）完整的上线服务！
	4、启动compose项目（docker-compose up .）
5.	默认的服务名：文件名_服务名_num
	1、多个服务器，集群，A,B,_num副本数量
	2、服务redis服务=>4个副本
	3、集群状态，服务都不可能只有一个运行实例，弹性，10高并发
	4、kubectl service负载均衡
6.	网络规则
	1、docker network ls
	2、项目中的内容都在同个网络下，可以通过域名进行访问如：mysql:3306 10个容器实例：mysql
	3、docker network inspect bridge 进行容器之间的访问
```

### 7.2	使用Docker Compose创建Flask应用

### 7.3	使用Docker Compose创建WordPress应用

```dockerfile
1.	网址：https://docs.docker.com/compose/wordpress
```



### 7.4	使用Docker-compose部署flask项目

```dockerfile
1、Dockerfile文件
    FROM python:3-alpine
    LABEL maintainer='15270265094@163.com'
    LABEL description='Deploying flask project'

    WORKDIR /usr/src/app

    COPY ./dev01 ./dev01/
    COPY ./gunicorn_config.py ./configs/

    WORKDIR dev01/

    RUN pip install --no-cache-dir -i https://pypi.douban.com/simple -r requirements.txt && \
    pip install -i https://pypi.douban.com/simple gunicorn && \
    mkdir ./logs/

    VOLUME /usr/src/app/logs/
    VOLUME /usr/src/app/dev01/

    EXPOSE 5000

    CMD ["/usr/local/bin/gunicorn", "-c", "/usr/src/app/configs/gunicorn_config.py", "run:app"]
2、docker-compose.yml文件
    # 在第一个区域下指定docker-compose版本
    version: '3.1'

    # 在第二个区域下定义容器
    services:

      db:
        image: mysql:5.7 # 下载或使用指定的镜像
        command: --default-authentication-plugin=mysql_native_password
        volume: # 指定volume，可以多个
          - dbdata:/var/lib/mysql
        restart: always
        environment:
          MYSQL_ROOT_PASSWORD: 123456
          MYSQL_DATABASE: dev01

      app:
        depends_on: # 指定容器的启动依赖关系，当db容器启动成功之后，再启动当前容器
          - db
        build: .
        image: keyou1/my_flask_app:latest
        ports: # 指定映射的端口
          - "5000:5000"
        volume:
          - mycode: /usr/src/app/dev01/
          - logs: /usr/src/app/logs/
        restart: always # 指定重启策略，但失败时自动重启，他的策略有restart:"no";restart:"always";restart:"on-failure";restart:"unless-stopped"
        environment:
          FLASK_APP: run.py

    volume: # 声明要创建的volumes
        dbdata:
        mycode:
        logs:

    # 运行docker-compose文件的话执行：
    1. 启动服务
            1、docker-compose -f docker-compose.yml up -d
    2.  停止服务
            1、docker-compose -f docker-compose.yml stop
    3.  停止并删除服务
            1、docker-compose -f docker-compose.yml down
```



## 8	Docker Swarm
### 8.1	简介
### 8.2	Swarm manager
### 8.3	Swarm work
### 8.4	创建集群
### 8.5	节点操作
### 8.6	service
### 8.7	stack
### 8.8	应用

## 9	总结

```dockerfile
1.	掌握：docker基础，原理，网络，服务，集群，错误排查，日志，linux docker k8s
```

