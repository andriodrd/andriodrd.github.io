# loadrunner

## 1. loadrunner常用函数

### web_url()

```c
# 发送一个get请求
web_url("login",
		"URL=http://192.168.2.27:8061/MCAOA/LEAP/Login/4403/MCAOA/Login.html",
		"TargetFrame=",
		"Resource=0",
		"Referer=",
		LAST);
```

### web_submit_data()

```c
# 发送一个post请求
# web_link()和web_url()函数都是页面访问型函数，实现HTTP请求中的GET方法，如果需要实现POST方法，可使用web_submit_form或web_submit_data()函数。当请求比较特别，VuGen无法使用以上4个函数时，便会采用web_custom_request()函数。
web_submit_data("login",
		"Action=http://192.168.2.27:8061/MCAOA/LEAP/Login/4403/MCAOA/Login.html",
		"Method=POST",
		"TargetFrame=",
		"Referer=",
		ITEMDATA,
		LAST);
```

### web_reg_find()

```c
# 设置检查点
# 注册函数都放在请求前面进行处理
```

### lr_eval_string()

```c、
# 读取一个变量的值为字符串
# 利用atoi()将字符转化为整形数值
# 通过{变量名}读取
```

### lr_log_message()

```c
# 输出日志信息
lr_output_message("size is %d",size);
```

### lr_reg_save_param_regrxp()

```c
# 关联找到数据并放在变量里面
需要通过正则表达式
```
### web_reg_save_param()

```c
# 这是最常用的参数化的形式
```

### web_reg_save_param_json()

```c
# 从响应body中提取数据到变量中
```

### lr_start_transaction()

```c
lr_end_transaction('login transaction',LR_AUTO)，自动的话是4XX和5XX才会出现fail
# 定义事务，开始和结束不准确，所以要结合检查点进行判断
lr_end_transaction('login_transaction',LR_PASS);
lr_end_transaction('login_transaction',LR_FAIL);
```

### 上下文相关的请求

```c
web_link()和web_submit_form()发送的请求依赖上下文的请求，因为他没有url_target
```

### web_set_proxy

```c
通过fiddler抓包loadrunner发送的请求
```

### web_add_header()

```c
添加HTTP信息头（比如Content-Type，token等）
```

### web_custom_request()

```c
使用web_custom_request()提交json数据
Body填入请求的json字符串，此处注意要转义
```
### web_save_header ()

```c
# web_reg_save_param ( "ResponseBody", "LB=", "RB=", "Search=Body", LAST );
   // REQUEST为内置变量，保存请求的头信息，需在发送URL请求前注册使用，将请求头信息存入字符串 RequestHeader 中
# web_save_header ( REQUEST , "RequestHeader" );
   // 将RESPONSE保存响应的头信息在 字符串 ResponseHeader 中
# web_save_header ( RESPONSE , "ResponseHeader" );
```



## 2. 检查点

检查点与事务相结合，正式环境事务的成功率大于99%

```c
// 检查点定义到变量中
	web_reg_find("Fail=NotFound",
		"Search=Body",
		"SaveCount=ceshi",
		"Text==",
		LAST);
// 检查点与事务结合
	if (atoi(lr_eval_string("{ceshi}"))==2) {
		lr_end_transaction("login_check",LR_PASS);
	}
	else {
		lr_end_transaction("login_check",LR_FAIL);
	}
```

## 3. 思考时间

模拟真实场景-lr_think_time()

- 思考时间的大小应该设置不同
- 不能设置太长，场景严于用户的真实场景，端标准就提升了

## 4. 集合点

适合场景

- 主要关注大用户并发的时候
  - 所有用户都在发请求
  - 所有用户都在提交一个请求

- 并发测试属于压力测试的一个子集
- 负载测试：评估性能指标（真实场景）
- 压力测试：做个瓶颈的预案（不需要思考时间）
- 并发测试：相对严格的并发，是来进一步解决问题的，无法得出一个综合的性能测试结果，但是可以进一步定位性能瓶颈问题产生的原因
- 稳定性测试：长时间标准用户数（最佳用户数-系统处于最佳状态，最大用户数-某一个指标出现极限）-内存泄漏、内存溢出问题内存未及时回收-内存到达上限的唯一处理方式就是系统崩溃
- 容量测试：模拟系统长时间允许的性能状态

```c
	// 事务的统计时间不包含集合的时间，并发测试

	// 集合点
	lr_rendezvous("start_login");

	// 开始事务 
	lr_start_transaction("login_check");
```



## 5. 调试

### 看请求与响应

```c
// 自定义相应体的数据到变量中
	
	web_reg_save_param("ResponseBody",
		"LB=",
		"RB=",
		"Search=Body",
		LAST);
	
	// REQUEST为内置变量，保存请求的头信息，需在发送URL请求前注册使用，将请求头信息存入字符串RequestHeader中
	web_save_header(REQUEST, "RequestHeader");

	// 将RESPONSE保存响应的头信息在字符串ResponseHeader中
	web_save_header(RESPONSE, "ResponseHeader");
//字符转码
	lr_convert_string_encoding(lr_eval_string("{RequestHeader}"),LR_ENC_UTF8,LR_ENC_SYSTEM_LOCALE,"RequestHeaderUTF8" );
	lr_convert_string_encoding(lr_eval_string("{ResponseHeader}"),LR_ENC_UTF8,LR_ENC_SYSTEM_LOCALE,"ResponseHeaderUTF8");
	lr_convert_string_encoding(lr_eval_string("{ResponseBody}"),LR_ENC_UTF8,LR_ENC_SYSTEM_LOCALE,"ResponseBodyUTF8");


	// 输出数据
    lr_output_message("# 请求头信息：\n %s", lr_eval_string("{RequestHeaderUTF8}"));
	lr_output_message("# 响应头信息：\n %s", lr_eval_string("{ResponseHeaderUTF8}"));
	lr_output_message("# 响应体信息：\n %s", lr_eval_string("{ResponseBodyUTF8}"));

```

### LR中 “simulate a new user on each iteration"设置项

问题描述：

- controller里为脚本设置了100个VU，设置每个VU的迭代次数为20次，正确运行时应该在系统中生成2000条记录，但从controller里面看到，运行时这100个VU都只运行了一次，最终生成的只有100条记录

问题分析：

- 开始我以为该问题是由脚本错误引起的，但经过对脚本的调试，验证确实没有问题，而且，在VUG中回放该脚本，回放多次就能生成多条记录，看来脚本的correlation等应该没有问题。仔细检查Web Server的日志，发现该日志中有多个访问“timeout.jsp”的访问记录，询问开发人员得知，在session超时时会访问这个页面。
- HTTP协议本身是无状态的，因此为了保留住sessionid，一般的做法是用hidden field、cookie或是在URL上附加sessionid来解决这个问题，查看LR记录的页面访问数据，可以看到，该应用是用cookie解决这个问题的：

解决方法：

- RuntimeSetting的设置，取消“simulate a new user on iteration”选项

## 6. Controller及场景设计

- 控制虚拟用户数量-负载生成器
- 控制性能测试场景-多少虚拟用户数才是合理的？多少时间是合理的
- 控制各种运行策略
- 附属功能：指标监控

### 负载生成器

- load generators看负载生成器有没有正常连接成功与否
- 设置百分比模式-设置场景到百分比模式-分配负载机

控制各种运行策略

- ip欺骗-最好是让服务器去掉Ip欺骗，或者设置一个万能的ip地址
  - 服务器对客户端的ip地址有验证要求
  - 要求ip地址不同
  - 仅适用于局域网，不适用于互联网，192.168.0.1~255（255个），或者更高级的网段172.168.1.1~255（6万多个）
  - 原理：一张网卡可以绑定多个ip地址

- 带宽的设置

## 7. 指标监控-analysis

-  对获取数据的加工与处理

- 真正施加压力确定服务器的性能状况

- 服务器在特定负载下的是否有瓶颈

- 无论好与不好，得出性能报告，有理有据阐述问题或者好做好心中有数

- 运行时间30分钟左右

- controller的事务响应时间是包含了思考时间的，而analysis里是忽略思考时间的（默认set filter）

### Summary Report

- Total Throughput(bytes)-总的吞吐量共接收了多少字节的数据
- Average Throughput(bytes/second)-平均吞吐量，可以用来衡量带宽是否达到了瓶颈
- Total Hits-总的请求点击数

### Graphs

#### Running Vusers

这张图本身没有任何价值，但是他最要的是作为关联，和其余图进行关联查看

#### 图标的右键使用

坚持的原则-ramp up/down

1. 越慢越好
2. 平衡好运行时间

- Merge Graphs

  合并图

- Auto Correlate

  自动关联图

####  性能指标-前端

- 响应时间，快不快
- 响应的吞吐率，每秒钟服务器的响应的大小
  - 服务端带宽
  - 客户端带宽
- TPS：每秒事务数，TPM->作用，设计性能需求
- HPS：每秒点击数，作用不大，减少HTTP请求                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         

#### 性能指标-后端

- CPU 

  1. CPU使用率 %Processsor Time，处理频率，内核数量，一级缓存，二级缓存，三级缓存的大小
  2. 处理器队列长度Processor Queue Length 最多为2*内核数

- 带宽

  1. 每秒接收的数据量，低于上行带宽/8
  2. 每秒发送的数据量，低于下行带宽/8

- 内存

  1. 内存使用率，可用内存数量（M）
  2. 内存的页交换频率page/sec，越低越好，内存和硬盘之间的数据交换
  3. 缓存，进行系统级性能优化的时候，重点利用好缓存机制；代码级性能优化，SQL语句，算法；减少内存，用好内存，减少运算次数
  4. 虚拟内存：利用一块硬盘区域模拟内存操作，内存比硬盘贵100倍，但是速度快100倍

- 磁盘IO

  1. 硬盘使用率
  2. 硬盘队列长度

- 线程池

  1. 多线程
     - cpu：多核多线程
     - 分布式应用服务器：多线程，每个线程负责处理一个用户请求
     - 数据库：多线程，每个线程处理一个SQL请求
  
  2. 线程池：用于管理多线程的一种机制
  3. 线程：主要消耗的是CPU资源，CPU资源是有限的
  4. 举例（动态影响）：
     
     - 3秒，60秒时间内可以发送20个，每发送一个帖子后，暂停2秒，请问60秒内可以发几个帖子？
       - 答案不是12个
     
     - 三层架构：C-S-D，瓶颈可能出现在任意一层，S端线程数量是100，调整成1000个，可以吗？
       - 可以也不可以
  

## 8. 性能测试方案

### <<DISCUZ VS Phpwind性能巅峰对决>>测试方案

#### 测试目标

1. 对比DISCUZ和Phpwind的性能，从性能角度对工具进行评估和选型提供一些数据上的参考

#### 测试过程

1. 测试计划：暂不适用于本文档
2. 测试方案：
   1. 用于指定测试策略，理清测试思路，为测试实施和执行提供技术上的参考
   2. 确定测试对象，测试场景和指标，避免在测试执行时临时抱佛脚

3. 测试实施：
   1. 搭建xampp服务器平台
   2. 安装Discuz和Phpwind论坛程序
   3. 开发LoadRunner测试脚本

4. 测试执行：
   1. 根据方案中制定的场景策略运行场景，并监控相关指标
   2. 根据相关指标进行分析，确定二者的性能，达成本次测试的目的

#### 测试对象

1. Discuz7.0.0 For PHP版

   现有帖子数：30377，现有回复数：15049，数据库大小：22.1M

2. Phpwind7.3.2

   现有帖子数：31064，现有回复数：15796，数据库大小：26.7M

#### 测试平台

1. 软件：

   Windowsx xp sp3 + xampp1.6.8

2. 硬件：

   CPU:P8400 2.26GHZ，MEM：3GB，网卡：千兆网卡

3. 客户端：由于机器限制，本次测试的客户端与服务器位于同一台电脑，由于只是对比测试，未牵涉到性能调优，所以硬件平台对于二者的结果判定是公平的
4. 测试工具：LoadRunner9.5英文版

#### 被测模块

1. 论坛登录
2. 论坛发帖
3. 帖子回复

#### 性能测试脚本开发方案

1. 总体方案：
   1. 手工定义事务，事务状态有LR自动判断，不适用web_reg_find进行手工检查（经预测试发现几个功能出错率很小，所以不考虑本身的健壮性，使脚本简单化）
   2. 只关注三个POST请求（使用web_submit_data函数提交POST请求），为减少测试结果的干扰因素，不考虑打开某个页面的性能（使用web_url函数提交的GET请求），不模拟真实用户需要打开首页，打开登录页面才能登录，或者必须进入某个模板才能发帖等真实操作
   3. 不适用集合点策略
   4. 为避免两个系统在运行性能测试时的干扰因素，要确保两个测试脚本完全一致，需要实现如下要求：
      1. 相同的参数设置
      2. 相同的事务定义
      3. 相同的关联
      4. 相同的思考时间设置
      5. 相同的场景设计
      6. 每执行一次测试，都重启一下xampp服务器，清理服务器环境

2. 论坛登录模块：
   1. 预先注册50个用户（密码相同），使用For循环来产生一个1-50的序列，注册后用户名为lruser1,lruser2...lruser50
   2. 从lruser1...lruser50中每次迭代随机取一个用户名（产生一个1-50的随机数并与lruser字符串一起即可）用作登录用户名
   3. 直接发送POST请求到服务器登录处理页面，不用先打开首页和登录页面

3. 论坛发帖模块：
   1. 需要使用关联来解决客户端验证问题
   2. 由于Phpwind使用UTF-8编码，而Discuz使用GBk编码，为降低影响因素，统一使用英语作为帖子的标题和内容（如果使用中文，在PHpwind上则需要使用lr_convert_string_encoding函数来动态装换中文内容，增加一个潜在的影响因素，所以不适用中文）
   3. 不考虑对多个板块进行发帖，专门新建一个板块来进行发帖测试
   4. 帖子标题和内容使用一个唯一数加上一段固定的英文的方式来产生不同的标题和内容

4. 帖子回复模块：
   1. 直接使用发帖操作中关联出来的客户端验证码
   2. 标题和内容设置与发帖相同
   3. 从本版块中随机抽取100个帖子作为回复的对象（100个被回复帖子的id号通过创建Random Number类型参数并制定具体范围来实现）

#### 性能测试场景设计方案

1. 由于客户端与服务器位于同一台电脑，经过预测试发现很容易达到100%，所以本次测试准备测试50和100个虚拟用户两种负载
2. 同时，也由于CPU使用率偏高的问题，我们为所有测试步骤都没有设置思考时间，统一设置思考时间为2秒，以此来降低CPU的负荷，并且将思考时间置于事务外，这样响应时间中将不包含思考时间（如将思考时间置于事务内也可以，那么记得在Analysis中将思考时间扣除）
3. 使用ramp up和ramp down策略，用于分析随着虚拟用户的增加两个系统在响应时间上的变化情况
4. 对于50个虚拟用户，ramp up设置为每30秒加5个用户，ramp down设置为每30秒减少5个用户
5. 对于100虚拟用户，ramp up设置为每30秒加10个用户，ramp down设置为每30秒减少10个用户
6. ramp up完成后持续时间为5分钟，这样总体运行时间约为15分钟
7. 不使用集合点，不适用ip欺骗，一次运行只测试一个脚本，负载生成器为本机

#### 重要分析指标

1. 监控CPU

   %Processor和Processor Queue Length两个指标

2. 监控内存

   总体内存可用数

3. Web页面的HPS（Hits Per Second）-每秒点击数，即每秒请求数

4. Web页面的吞吐量（Throughput）

5. 登录，发帖和回复的TPS（Transaction Per Second）-每秒事务数

6. 帖子数和回复数

   相同场景下比较二者发送成功的帖子数量和回复数量，量多者胜

#### 其他考虑

1. Discuz和Phpwind自身都带了一些针对不同用户数量的优化参数供使用，对比测试不使用任何优化设置，保持默认的设置即可
2. Discuz和Phowind对发帖时间间隔有限制，请取消该限制，确保发帖没有时间间隔
3. 使用机器名或真实的IP地址访问服务器而非127.0.0.1或localhost
4. 为避免Apache和MySql内存释放不完全，每次测试完后重启一下XAMPP
5. 由于需要测试登录模块，所以需要确保每次运行完成后都要将该用户登出，否则就会存在下一迭代时被告知用户已经登录的情况





  

