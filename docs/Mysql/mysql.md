# mysql

## 1.	索引的本质

> 1. 索引是帮助Mysql高效获取数据的排好序的数据结构
> 2. 索引数据结构（https://www.cs.usfca.edu/~galles/visualization/Algorithms.html）
>    - 二叉树
>    - 红黑树
>    - Hash表
>    - B-Tree

### 1.1	MySAM索引

> 1. MySAM索引采取的是B+Tree的数据结构，在`mysql8/data/数据库名/`目录下会形成3个文件，一个是`.frm`的文件存放的是表结构，一个是`.MYD`的文件存放的是数据，一个是`.MYI`的文件存放的是索引；
> 2. MySAM索引是`非聚集的`，即索引文件和数据文件是分离的，要先去索引文件中查询到磁盘地址，再去数据文件中取数据；
> 3. B+树相关知识：
>    - 非叶子节点不存储data，只存储索引（冗余），可以放更多的索引
>    - 叶子节点包含索引索引字段
>    - 叶子节点用指针连接，提高区间访问的性能

### 1.2	InnoDB索引

> 1. InnoDB是mysql目前用的索引方式（聚聚）
> 2. 表结构文件本身就是按B+Tree组织的一个索引结果文件
> 3. 聚集索引-叶节点包含了完整的数据记录
> 4. 产生的文件为`.frm`表结构文件，`.ibd`索引和数据的文件
> 5.  因为InnoDB的数据文件本身要按主键聚集，所以InnoDB要求表必须有主键(MyISAM可以没有)，如果没有显式指定，则mysql会自动选择一个可以唯一标识数据记录的列作为主键。如果不存在这种列，则mysql自动为InnoDB表生成一个隐含字段作为主键，这个字段长度为6个字节，类型为长整型

### 1.3	场景选择

> 1. MyISAM不需要事务支持（不支持）并发相对较低（锁定机制问题）数据修改相对较少（阻塞问题），以读为主数据一致性要求不是非常高
> 2. InnoDB需要事务支持（具有较好的事务特性）行级锁定对高并发有很好的适应能力，但需要确保查询是通过索引完成数据更新较为频繁的场景数据一致性要求较高硬件设备内存较大，可以利用InnoDB较好的缓存能力来提高内存利用率，尽可能减少磁盘 IO

## 1.4	介绍几个名词

> 1. 回表 （回到原来主键的那张表里面去了嘛）
>    - 先去普通的b+树里面进行查找，查找到的是主键的id，再去主键的B+树进行查找，查找正在想要的数据。
> 2. 覆盖索引
>    - 先去普通的b+树里面进行查找，查找到的是主键的id即为想要的数据，不再走主键的表，只需要主键的id值即可
> 3. 索引的分类
>    - 主键
>    - 唯一键
>    - 普通索引
>    - 全文索引
>    - 组合索引
>
> 4. 最左匹配原则
>    - （name ，age）建立组合索引

## 2.	Mysql的基本逻辑结构示意图

![image-20210122173225852](assets/image-20210122173225852.png)

> 备注：
>
> 1. Mysql架构分为3层，分别为客户端，Server层，存储引擎层
> 2. 优化器优化sql语句，规定执行流程，生成索引和执行计划，供执行器来执行计划
> 3. 执行器为SQL语句实际执行的组件（如select from where group by having等关键字来分组sql语句，来形成执行计划），和调用存储引擎层的接口来获取查询结果
> 4. 连接器来控制用户的连接（是立即连接还是队列等待）和权限验证
> 5. 分析器来进行词法分析和语法分析sql语句正确与否
> 6. 存储引擎层来放不同的文件格式的存储（由于不同的存储引擎），innodb存储在磁盘中，MylSAM存储在磁盘中，memory内存中。`show engines查看存储引擎层支持的类型`

## 3.	Mysql缓存机制

> 1. 缓存数据形式key-value,，key为查询SQL
> 2. 查询缓存在5.7之后版本被移除
> 3. 使用场景-读多写少，准确来说是几乎不怎么写
> 4. 需要在my.cnf中配置query_cache_type参数（0.1.2）
> 5. 按需使用缓存select SQL_CACHE * from test
> 6. 查看缓存运行状态：show status like "%Qcache%"

## 4.	Bin Log

### 4.1	Bin Log理解

> 1. 二进制日志文件，主从复制的时候是必开启的
> 2. binlog在mysql的server层实现（引擎公用）
> 3. binlog未逻辑日志，记录的是一条语句的原始逻辑，不是sql语句
> 4. binlog不限大小，追加写入，不会覆盖以前的日志

### 4.2	Bin Log设置

> 1. 开启bin-log
>    - 查看bin-log是否开启：`show variables like "%log_bin%"`
>    - `log-bin=/var/lib/mysql/mysql-bin`用来开启二进制日志文件
>    - 注意5.7以及更高版本需要配置本项：server-id=123546，同来指定唯一id，不同的mysql实例不重复即可
>
> 2. bin-log命令
>    - `sync_binlog`：设置为1，表示每次事务binlog都会将持久化到磁盘
>    - flush logs：会多一个最新的bin-log日志
>    - show master status：查看最后一个bin-log日志的相关信息
>    - reset master：清空所有的bin-log日志
>    - `/usr/local/mysql/bin/mysqlbinlog --no-defaults /usr/local/data/mysql-bin.00001`查看bin-log内容
>    - 恢复指定位置段数据 `--stop-position = "100" --start-position="50"(看的是end_log_pos )`
>    - 恢复指定日期段数据 `--stop-date="2019-03-01 12:00:00" --start-date="2018-03-02 11:55:00"`
>    - `binlog-do-db=wd_msg`指定需要复制的数据库
>    - `replicate-do-db=wd_msg`需要同步的数据库名称，与master上的配置保持一致
>    - `binlog_format`=（STATEMENT，ROW，MIXED中的任意一个）：mysql复制主要有三种方式，给予sql语句的复制（statement-based-replication,sbr）对应的，binlog的格式也有三种，statement,row,mixed
>      - statement：每一条会修改数据的sql语句会记录到binlog中。优点是并不需要记录每一条sql语句和每一行的数据变化，减少了binlog日志量，节约IO，提高性能。缺点是在某些情况下会导致master-slave中的数据不一致(如sleep()函数， last_insert_id()，以及user-defined functions(udf)等会出现问题)
>      - row：不记录每条sql语句的上下文信息，仅需记录哪条数据被修改了，修改成什么样了。而且不会出现某些特定情况下的存储过程、或function、或trigger的调用和触发无法被正确复制的问题。缺点是会产生大量的日志，尤其是alter table的时候会让日志暴涨。
>      - mixed：以上两种模式的混合使用，一般的复制使用STATEMENT模式保存binlog，对于STATEMENT模式无法复制的操作使用ROW模式保存binlog，MySQL会根据执行的SQL语句选择日志保存方式。

## 5.	Redo Log

### 5.1	Redo Log理解

>1. 记录InnoDB存储引擎的事务日志
>2. MySQL的WAL机制（Write-Ahead-Loggine），先写到日志再写磁盘
>3. 文件名`ib_logfile*`
>4. redo log又称重做日志文件，用于记录事务操作的变化，记录的是事务修改之后的值，不管事务是否提交都会记录下来。在实例和介质失败时，redo log文件就能派上用场，如数据库掉电，Inno db存储引擎会使用redo log恢复到掉电前的时刻，以此来保证数据的完整性。
>
>5. 批量写入日志。日志文件并不是直接写入文件，而是先写入redo log  buffer，当需要将日志刷新到磁盘时（如事务提交），将许多日志一起写入磁盘
>6. 并发的事务共享redo log的存储空间，他们的redo log按语句的执行顺序，依次交替的记录在一起，以减少日志占用的空间，也因为是依次交替的记录在redo log日志文件上，当一个事务将redo log写入磁盘时，也会将其他未提交的事务的日志写入磁盘
>7. redo log上只进行顺序追加的操作，当一个事务需要回滚时，它的redo log记录也不会从redo log中删除掉

### 5.2	Redo Log执行流程图

> 1. Redo Log写入原理
>    - 以循环方式写入日志文件，不断的写与擦除，文件1写满时，切换到文件2，文件2写满时，再次切换到文件1。
>    - `writepos`当前记录的位置，循环边写边后移
>    - `checkpoint`当前要擦除的位置，循环边写边后移

### 5.3	Redo Log参数设置

> 1. MySQL设置Redo Log
>    - 关于`innodb_log_buffer_size`的大小：（默认80M）
>    - `innodb_log_file_size`重做日志文件的大小，默认48M；
>    - `innodb_log_file_in_group`指定重做日志文件组中文件的数量，默认2，如默认的`ib_logfile0`和`ib_logfile1`
>    - `innodb_mirror_log_groups`指定了日志镜像文件组的数量，默认1，次功能属于未实现的功能，在5.6版本中废弃，在5.7版本中删除了；
>    - `innodb_log_group_home_dir`指定了日志文件组所在的路径，默认./ ，表示在数据库的数据目录下
>    - `innodb_flush_log_at_trx_commit`设置commit时如何将log buffer中的日志刷log file中（值0，1，2）默认为1
>    - 查看命令`show variables like '%innodb%log%'`
>
> 2. redo log怎么设置
>
>    - redo log 文件的大小设置对于innodb存储引擎的性能有这非常大的影响
>    - 设置的太大
>      - 设置很大以后减少了checkpoint，并且由于redo log是顺序i/o，大大提高了i/o性能。但是如果数据库意外出现了问题，比如意外宕机，那么需要重放日志并且恢复已经提交的事务，如果日志很大，那么将导致恢复时间很长。甚至到我们不能接受的程
>
>    - 设置的太小
>      - 当一个日志文件写满后，innodb会自动切换到另一个日志文件，而且会触发数据库的检查点（checkpoint），这回导致innodb缓存胀页的小批量刷新，会明显降低innodb的性能。
>
>    - 设置
>
>      - 把重做日志设置很大，甚至与缓冲池一样大（硬件恢复速度上来了）
>
>      - 考虑增加日志缓冲区的大小
>
>      - 设置innodb_log_write_ahead_size的参数，表示redo log写前的块大小与文件系统的大小一致，4096字节一般。
>
>        



### 5.3	Flush Log原理

![image-20210125180115368](assets/image-20210125180115368.png)

## 6.	Mysql redo log和bin log 的区别

> 1. 预备知识：
>
>    - 数据库的语言分类
>      - 一般常用的DML:数据操纵语言，对表的数据进行操作（insert，update，delete）语句和DCL：数据库控制语言（创建用户，删除用户，授权，取消授权）和DDL：数据库定义语言，对数据库内部的对象进行创建，删除，修改的操作，均是使用mysql提供的公共接口mysql_execute_command，来执行相应的sql语句。
>
>    - undo日志与redo日志
>      - undo日志用于记录事务开始前的状态，用于事务失败时的回滚(`即该事务未被执行commit命令。但是此时，该事务修改的脏块中也有可能一部分脏块写入到数据文件中了。如果此时数据库实例崩溃了，则当数据库实例恢复时，就需要用回滚（这个机制）来将先前那部分已经写入到数据文件的脏块从数据文件上撤销掉`)操作；redo日志记录事务执行后的状态，用于恢复未写入（`即该事务已经被执行commit命令了，只是现在该事务修改所对应的脏数据块中只有一部分被写到磁盘上的数据文件中，还有一部分已经被置为提交标记的脏块还在内存上，如果此时数据库实例崩溃了，则当数据库实例恢复时，就需要用前滚（这个机制）来完成事务的完全提交，即将先前那部分已经被置为提交标记且还在内存上的脏块写入到磁盘上的数据文件中`）date file的已成功事务更新的数据。
>    - 事务执行的各个阶段
>      - 写undo日志到log buffer
>      - 执行事务，并写redo日志到log buffer
>      - 如果innodb_flush_log_at_trx_commit=1，则将redo日志写到log file，并刷新落盘
>      - 提交事务
>
>    - 事务
>
>      - mysql提供了多种存储引擎来支持事务。支持事务的存储引擎有Innodb和bdb，其中，Innodb存储引擎主要通过undo日志和redo日志实现，mylsam存储引擎不支持事务。
>
>      - sql使用下列语句来管理事务
>
>        - 引入事务的一个原因：为了有效保证并发读取数据的正确性，提出了事务的隔离级别，在mysql中，事务的隔离级别是repeatable-read(可重读)隔离级别，即事务未结束时（未执行commit或rollback时），其他会话只能读取到未提交数据
>
>        - 开始事务：begin或start transaction这个语句显示地标记一个事务的起始点
>        - 提交事务：commit表示提交事务，即提交事务的所有操作，具体地说，就是讲事务中所有对数据库的更新都写到磁盘上的物理数据库中，事务正常结束；提交事务，意味着将事务开始以来所执行的所有数据都修改成为数据库的永久部分，因此也标志着一个事务的结束。一旦执行了改命令，将不能进行回滚事务，只有在所有修改都准备好提交给数据库是，才执行这一操作
>        - 回滚（撤销）事务：rollback表示撤销事务，即在事务运行的过程中发生了某种故障，事务不能继续执行，系统将事务中对数据库的所有已完成的操作全部撤销，回滚到事务开始时的状态。这里的操作指对数据库的更新操作
>        - 总结：begin或start transaction语句后面的sql语句对数据库数据的更新操作都将记录在事务日志中，直至遇到rollback语句或commit语句，如果事务中某一操作失败且执行了rollback语句或commit语句，那么在开启事务语句之后所有更新的数据都回滚到事务开始前的状态。
>        - （mysql事务是一项非常消耗资源的功能）要求：
>          - 事务尽可能简短：事务的开启到结束会在数据库管理系统中保留大量资源，以保证事务的原 子性，一致性，隔离性和持久性。
>          - 事务中访问的数据量尽量最少：当并发执行事务处理时，事务操作的数据量越少，事务之间对相同数据操作就越少
>          - 查询数据时尽量不要使用事务
>          - 在事务处理过程中尽量不要出现等待用户输入的操作：在处理事务的过程中，如果需要等待用户输入数据，那么事务会长时间地占用资源，有可能造成系统阻塞。
>
>    - 恢复
>
>      - 恢复策略：redo log中记录了未提交的事务和回滚了的事务，因此在进行回复时，这些事务要进行特俗的处理，有两种不用的回复策略
>        - 进行恢复时，只重做已经提交了的事务
>        - 进行恢复时，重做所有事务包括未提交的事务和回滚了的事务，然后通过undo log回滚那些未提交的事务
>
>      - innoDB存储引擎的恢复机制：Mysql存储引擎中innoDB存储引擎使用了B策略，innoDB存储引擎中的恢复机制有几个特点：
>        - 在重做redo log时，并不关心事务性。恢复时，也没有begin，也没有commit，rollback的行为。也不关心每个日志是哪个事务的。尽管事务id等事务相关的内容会记录redo log，这些内容只是被当做要操作的数据的一部分。
>        - 使用b策略就必须要将undo log持久化，而必须要在写redo log之前将对应的undo log写入磁盘，undo 和redo的这种关联，使得持久化变得复杂起来。为了降低复杂度，innodb将undo log看做数据，因此记录undo log的操作也会被记录到redo log，这样undo log就可以像数据一样缓存起来，而不用在redo log之前写入磁盘了。
>
> 2. 区别：
>    - 第一：redo log是在innodb存储引擎层产生的，而binlog是mysql数据库的上层产生的，并且二进制日志不仅仅针对innodb存储引擎，Mysql数据库中的任何存储引擎对于数据库的更改都会产生二进制日志
>    - 第二：两种日志记录的内容形式不同。mysql的binlog是逻辑日志，其记录的是对应的sql语句，而innodb存储引擎层面的重做日志是物理日志
>    - 第三：两种日志与记录写入磁盘的时间点不同，二进制日志只是在事务提交完成后进行一次写入。而Innodb存储引擎的重做日志在事务进行中不断地写入，并且日志不是随事务提交的顺序进行写入的。二进制日志仅在事务提交时记录，并且对于每一个事务，仅包含对于事务的一个日志。而对于innodb存储引擎的重置日志，由于其记录的是物理操作日志，因此每个事务对应多个日志条目，并且事务的重做日志写入是并发的，并非在事务提交时写入，其在文件中记录的顺序并非是事务开始的顺序
>    - 第四：binlog不是循环使用，在写满或者重启之后，会生成新的binlog文件，redo log 是循环使用
>    - 第五：binlog可以作为恢复数据使用，主从复制搭建，redo log作为异常宕机或者介质故障后的数据恢复使用

```shell
grant all privileges on *.* to 'root'@‘%’ identified by 123456 grant options;
flush privileges;
```

## 7.	性能测试之mysql

## 7.1	哪些东西会影响mysql的性能

> 1. 硬件
> 2. 系统配置
> 3. 数据库表结构
> 4. SQL以及索引







































