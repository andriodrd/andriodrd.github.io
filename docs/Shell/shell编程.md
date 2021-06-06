# shell 编程

## 1	后续课程说明

### 1.1	到底什么是测试开发？

```shell
1. 一定不仅是测试
	1、非常敏锐的开发视角
	2、极其强大的开发能力
2. 一定不是开发
	1、测试平台不是产品，而是工具
	2、无需严谨校验
	3、无需完美的用户体验
```

### 1.2	测试开发的价值、理念

```shell
1. 效率至上
	1、一个月一个测试平台，一个工具
2. 能屈能伸
```

### 1.3	测试开发课程到底学什么？

### 1.4	如何提问？

## 2	shell 简介

### 2.1	shell是什么？

```shell
1. 命令解释器
2. shell就是在操作系统和应用程序之间的一个命令翻译工具
```

### 2.2	shell的分类

```shell
1. windows系统
	1、cmd.exe命令提示字符
2. linux系统
	1、sh /bash /zsh ...
```

## 3	常用命令

### 3.1	head

```shell
1. 默认获取文件前10行
	head --bytes=1000 /etc/passwd // 获取前1000字符的文件
```

### 3.2	tail

```shell
1. 默认获取文件后10行

2. 练习：获取一个文件第12-15行的内容
head -15 /etc/passwd | tail -4
```

### 3.3	cut

```shell
1. 取出文本指定的列
2. 默认以空格或者tab键进行分隔（不支持不规则的空格）
3. 选项
	1、-d指定分割符
	2、-f指定获取的列号
	head -6 /etc/passwd | tail -4 | cut -d':' -f1,3
```

### 3.4	uniq

```shell
1. 去除重复的内容
2. 选项
	1、-d仅打印有重复的元素（duplicate）
	2、-c打印元素重复的个数
3. 一般是先排序再去重 |sort|uniq
```

### 3.5	sort

```shell
1. 对文本的内容进行排序
2. 默认以字符的ASCII码数值从小到大排序
3. 选项
	1、-n以数值大小排序
	2、-r倒叙
	3、-t指定分隔符，默认为空格
	4、-knum指定以某个字段来排序
```

### 3.6	wc(word count)

```shell
1. 计算文本数量
2. 选项
	1、wc -l 打印行数
	2、wc -w 打印单词数
	3、wc -c 打印字节数
	4、wc -L 打印最长行的字节数
```

## 4	变量

### 4.1	分类

```shell
1. 本地变量
2. 全局变量
3. 内置变量
```

### 4.2	定义变量

```shell
1.	定义变量的=两边不能含空格，否则变量会被认定为是一个变量
	1、变量名=变量值
	2、变量值必须是一个整体，中间没有特殊字符
	3、等号两侧不能有空格
	4、特殊：变量名=$(linux命令)
	5、上述定义的变量默认为本地变量
username='muzhi'
2. 访问变量
	1、echo username
	2、echo $username
```

### 4.3	全局变量

```shell
1. 可以通过命令查看环境变量（只显示全局变量）
	1、env
2. 定义全局变量
	1、方式一
		变量=值
		export 变量
	2、方法二（最常用）
		export 变量=值
	3、方法三
		直接在vscode下得.bashrc文件中添加export 变量=值
```

### 4.4	查看变量

```shell
1. 方式一
	1、echo $变量名
2. 方式二
	1、echo ”$变量名“，必须要用到双引号，	单引号的话什么传进去，什么就传出来。
	2、常用方法
```

### 4.5	内置变量

```shell
1. 符号： 含义
	1、 $0: 获取当前执行的shell脚本文件名，包括脚本路径
	2、 $n: 获取当前执行的shell脚本的第n个参数，n=1...9 ，如果n大于9就要用大括号括起来${10}
	3、 $#: 获取当前shell命令行中参数的总个数
	4、 $?: 获取执行上一个指令的返回值（0为成功，非0为失败）
	5、 $*: 获取shell脚本的所有参数
```

## 5	数值运算

```shell
1. 支持的运算
	+ - * / %
	< <= > >=
	= != 
2. 方式一
	1、$((算数表达式))
	2、变量可以不加$
3. 方式二
	1、expr： 算数表达式
		echo "$(expr 20 \* 3)"
		echo $(expr 20 \* 3)
		二者一样的效果
```

## 6	条件表达式

### 6.1	返回值

```shell
1.	返回值
	1、条件成立，返回0
	2、条件不成立，返回1
```

### 6.2	逻辑表达式

```shell
&& 和 ||
```

### 6.3	文件表达式

```shell
-f: 判断输入内容是否是一个文件
-d: 判断输入内容是否是一个目录
-x: 判断输入内容是否可执行
-e: 判断文件是否存在
```

### 6.4	数值操作符

```shell
1. n1 -eq n2 相等
2. n1 -gt n2 大于
3. n1 -it n2 小于
4. n1 -ne n2 不等于
```

### 6.5	字符串比较

```shell
1. str1 == str2 str1和str2字符串内容一致
2. str1 != str2 str1和str2字符串内容不一致，!表示相反的意思
```

## 7	shell脚本格式

### 7.1	格式要求

```shell
1. 脚本前必须加上#！/bin/bash
举例：
		#！/bin/bash
		#Author：MingMing
		#Date: 2021-05-14
		#for test
2. shell脚本文件后缀，建议命名为.sh
3. 脚本执行失败时，使用exit返回非0值，来退出程序
4. shell脚本的命令简单、有意义
```

### 7.2	注释

```shell
1. 单行注释
	1、#
2. 多行注释
	1、
	：<<!
	这是注释
	！
```



总结：

​	1、一个（）里面是linux命令

​	2、两个（（））里面是算数表达式，算符运算符需要加\转移符。

​	3、一个{}里面是变量

​	4、两个{{}}不知道什么意思

​	5、[运算符左右两侧要有空格]里面写条件表达式 ：&& 和 ||

## 8	函数

### 8.1	格式

```shell
# 函数可以不加关键字function声明但是函数名后面必须加（），加了function后函数名后面（）可以不加
function name () {
    echo "$1" # arguments are accessible through $1, $2,...
}

# 对于echo中特殊字符的转义的使用，比如换行/n的话，使用echo -e ""
greet() {
    echo "haha"
}
```

### 8.2	参数

```shell
1.	函数体调用参数
	函数名（）{
		函数体 $n
	}
2.	调用函数给函数传参数
	函数名 参数
```

### 8.3	input

```shell
1.	read
2.	read value
3.	read -p prompt value
4.	备注
	如果你不写变量，那么默认的就会被$REPLY这个内置的变量接受参数
```

## 9	流程控制

### 9.1	if

```shell
1.	格式
if [ 条件1 ]; then
     # if body
elif [ 条件2 ]; then
     # else if body
else
     # else body
fi
```

### 9.2	for

```shell
1.	格式一
for 值 in 列表
do
	执行语句
done

2.	格式二
for((i=0;i<n;i++)); do
    echo "${i}"
done
```

### 9.3	while

```shell
1.	格式
	只要条件满足，就一直循环
    while [ condition ]; do
        # body
    done

```

### 9.4	until

```shell
1.	格式
	只要条件不满足，就一直循环
	until 条件
	do
		执行语句
    done
```

### 9.5	case

```shell
1.	格式
case 变量名 in
	值1）
		指令1
			；；
	值2）
		指令2
			；；
	值3）
		指令3
			；；
	*）
		指令4
			；；
esac
```



## 10	文本处理三剑客(给简历添砖加瓦)

### 10.1	grep

```shell
1.	grep：查询文本内容含有指定模式的行，返回的是文本的一行内容
2.	参数
	-i: 忽略大小写
	-c: 计算含有匹配模式的行数
	-r: 递归查询目录下含有指定模式的文件
	-n: 打印行号s
	-l: 只显示文件名，不显示匹配指定模式的内容，常常配合-r一起使用
	-W:	高级的正则表达式匹配模式
	-F:	不支持正则，按字符串字面意思进行匹配
3.	综合案列
	1.	grep '/usr/sbin/nologin' /etc/passwd | cut -d":" -f1
	2.	grep -i 'keyou' testcases.log | grep 'CRITICAL' | wc -l
	3.	grep "$USER" /etc/passwd $> /dev/null && echo "$USER existed." || echo "$USER not existed."
		// 备注：垃圾箱 $> /dev/null 不管命令是否正确都扔到垃圾箱中去
```

### 10.2	sed

```shell
1.	流编辑器，对文件逐行进行处理
2. 	两种形式
	1、sed [option] "pattern command" file
	2、some command | sed [option] "pattern command"
3.	选项：含义
	-n: 只打印模式匹配的行，不然的话会打印2遍，一遍是原始的内容，一遍是搜索的内容。
	-f: 加载存放动作的文件，即将-f设定为指定模式（echo "3,9 s/bin/BIN/g" > sed_test.sed）,sed -f sed_test.sed passwd 即加载"3,9 s/bin/BIN/g"这个模式去匹配。
	-r: 支持拓展正则
	-i: 直接修改文件,做真正的修改。
4.	pattern模式
	1、匹配模式：含义
		5：只处理第5行
		5,10：只处理第5行到第10行
		/pattern1/: 只处理能匹配pattren1的行
		/pattern1/,/pattern2/: 只处理从匹配pattern1的行到匹配pattern2的行
5.	command命令
	1、查询
		1. p
			打印
	2、新增
		1. a: 在匹配行后新增
		2. i: 在匹配行前新增
		3. r: 外部文件读入，行后新增
		4. w: 匹配行写入外部文件
	3、删除
		1. d
	4、修改
		1. s/old/new: 只修改匹配行中第一个old
		2. s/old/new/g: 修改匹配行中所有的old
		3. s/old/new/ig: 忽略大小写
6.	例子
	1、sed -n '2 p' /etc/passwd
	2、sed -n '/mail/ p' /etc/passwd
	3、sed -n '/^list/,/^message/ p' /etc/passwd
	4、sed '/^list/,/^message/ r test.txt' passwd\
	5、sed '/\/usr\/sbin\/nologin/ w one_file.txt' passwd
	6、sed '3,9 s/bin/BIN/' passwd | cat -n
	7、cat /proc/cpuinfo | sed -n '/cpu cores/ p' | uniq | cut -d" " -f3
	
```

### 10.3	awk

```shell
1.	文本处理工具，处理数据并生成结果报告
2.	两种形式
	1、awk 'BEGIN{}pattern{commands}END{}' file
	2、some command | grep [option] [pattern]
3.	格式：含义
	1、BEGIN{}: 处理数据之前执行
	2、pattern: 匹配模式（只支持如下的两种匹配模式）
		/pattern1/: 只处理能匹配pattren1的行
		/pattern1/,/pattern2/: 只处理从匹配pattern1的行到匹配pattern2的行
	3、{commands}: 处理的命令
	4、END{}: 处理数字之后执行
4.	内置变量
	1、$0: 整行内容
	2、$1~$n: 当前行的第1~n个字段
	3、NF(Number Field): 当前行字段数
	4、NR(Number Row): 当前行行号，从1开始
	5、FS(Field Separator): 输入字符分隔符，默认为空格或tab键
	6、RS(Row Separator): 输入行分隔符，默认为回车符
5.	printf格式符
	1、%s: 字符创
	2、%d: 十进制数字
	3、%f: 浮点数
	4、+： 右对齐
	5、-： 左对齐
6.	例子
	1、awk '{print $1}' awk_test01.txt
	2、awk '{print NF}' awk_test01.txt
	3、awk '{print $NF}' awk_test01.txt
	4、awk '{print RF}' awk_test01.txt
	5、awk '{print $RF}' awk_test01.txt
	6、awk 'BEGIN{FS=":"}{print $1,$NF}' /etc/passwd
	7、awk 'BEGIN{FS=":"}{printf "%s%s", $1,$NF}' /etc/passwd 
	8、awk 'BEGIN{FS=":"}{printf "%-20s%-20s\n", $1,$NF}' /etc/passwd
  	9、awk 'BEGIN{FS=":"} /^root/ {printf "%-20s%-20s\n", $1,$NF}' /etc/passwd
  	10、awk 'BEGIN{FS=":"} $6 == "/bin" {printf "%-20s%-20s\n", $1,$NF}' /etc/passwd
  	11、awk 'BEGIN{FS=":"} $6 ~ "/bin" {printf "%-20s%-20s\n", $1,$NF}' /etc/passwd
  	12、awk 'BEGIN{FS=":";printf "%s-20s%-20s\n","Username","Shell"} $6 ~ "/bin" {printf "%-20s%-20s\n", $1,$NF}END{printf "%-20s%-20s\n","Total",count}' /etc/passwd
7.	awk文件
1.#test1.awk
    BEGIN {
        printf "%-10s%-10s%-10s%-10s%-10%s\n", "Tester", "ErrorTotal", "CriticalTotal", "PassTotal", "FailTotal"
    }

    {
        if($5 == "Pass")
        {
            SUCCESS[$4] += 1
        } else
        {
            FAIL[$4] += 1
        }
        if ($3 == "ERROE"){
            ErrorTotal[$4] += 1
        }
        if ($3 == "CRITICAL"){
            CriticalTotal[$4] += 1
        }
        USERS[$4] += 1
    }

    END {
        for (u in USERS){
            ALL_ERROR+=ERROE[u]
            ALL_CRITICAL+=CRITICAL[u]
            ALL_SUCCESS+=SUCCESS[u]
            ALL_FAIL+=FAIL[u]
        }
        printf "%-10s%-10s%-10s%-10s%-10%s\n", "Total", ALL_ERROR, CRITICAL[u], SUCCESS[u], FAIL[u]

    }

    # 使用：awk -f test1.awk /etc/passwd
2.#test2.awk
    BEGIN {
        printf "%-10s%-10s\n", "Tester", "TotalTestcases"
    }

    {
        if ($4 == "keyou")
        {
            keyou ++
        }else if ($4 == "keke")
        {
            keke ++
        }
    }

    END {
        printf "%-10s%-10s\n", "keyou", keyou
        printf "%-10s%-10s\n", "keke", keke
    }

    # 使用：awk -f test2.awk /etc/passwd
3.#test3.awk
    BEGIN {
        printf "%-10s%-10s\n", "Tester", "TotalTestcases"
    }

    {
        USERS[$4] += 1
    }

    END {
        for (u in USERS)
            printf "%-10s%-10d\n", u, USERS[u]
    }

    # 使用：awk -f test3.awk /etc/passwd
4.#test4.awk
    BEGIN {
        printf "%-10s%-10s-10%s\n", "Tester", "PassTotal", "FailTotal"
    }

    {
        if($5 == "Pass")
        {
            SUCCESS[$4] += 1
        } else
        {
            FAIL[$4] += 1
        }
        USERS[$4] += 1
    }

    END {
        for (u in USERS)
            printf "%-10s%-10s-10%d\n", u, SUCCESS[u], FAIL[u]
    }

    # 使用：awk -f test4.awk /etc/passwd
```

## 11	日志分析综合演练

### 11.1	统计每位测试人员执行用例的总数

```shell
BEGIN {
    printf "%-10s%-10s\n", "Tester", "TotalTestcases"
}

{
    USERS[$4] += 1
}

END {
    for (u in USERS)
        printf "%-10s%-10d\n", u, USERS[u]
}

# 使用：awk -f test1.awk /etc/passwd
```

### 11.2	分别统计每位测试人员执行用例成功和失败的总数

```shell
BEGIN {
    printf "%-10s%-10s-10%s\n", "Tester", "PassTotal", "FailTotal"
}

{
    if($5 == "Pass")
    {
        SUCCESS[$4] += 1
    } else
    {
        FAIL[$4] += 1
    }
    USERS[$4] += 1
}

END {
    for (u in USERS)
        printf "%-10s%-10s-10%d\n", u, SUCCESS[u], FAIL[u]
}
```

### 11.3	将1、2合并，同时分别统计每位测试人员执行用例CRITICAL、ERROR日志等级数以及所有测试人员每项总数

```shell
BEGIN {
    printf "%-10s%-10s%-10s%-10s%-10%s\n", "Tester", "ErrorTotal", "CriticalTotal", "PassTotal", "FailTotal"
}

{
    if($5 == "Pass")
    {
        SUCCESS[$4] += 1
    } else
    {
        FAIL[$4] += 1
    }
    if ($3 == "ERROE"){
        ErrorTotal[$4] += 1
    }
    if ($3 == "CRITICAL"){
        CriticalTotal[$4] += 1
    }
    USERS[$4] += 1
}

END {
    for (u in USERS){
        ALL_ERROR+=ERROE[u]
        ALL_CRITICAL+=CRITICAL[u]
        ALL_SUCCESS+=SUCCESS[u]
        ALL_FAIL+=FAIL[u]
    }
    printf "%-10s%-10s%-10s%-10s%-10%s\n", "Total", ALL_ERROR, CRITICAL[u], SUCCESS[u], FAIL[u]

}

# 使用：awk -f test1.awk /etc/passwd
```

### 11.4	使用shell脚本来实现，并显示菜单，选择不同的选项则打印不用的内容

```shell
1.	作业
```

### 11.5	使用shell脚来部署项目

```shell
#!/bin/bash
#Author：MingMing
#Date: 2021-05-14
#deploy flask

#1. 打包

#2. 上传

#3. 解压
PROJECT_DIR="$HOME/Projects"
PROJECT_HOME="dev01"
CODE_PATH="$HOME/dev01.tar.gz"
function unpack_code () {
    if [ ! -e "${PROJECT_DIR}" ]; then
    {
        echo "${PROJECT_DIR} isn't existed"
        mkdir -p ${PROJECT_DIR} && echo "created ${PROJECT_DIR} successfully!"
    }
    fi
    cd "${PROJECT_DIR}"
    tar -zvxf "${CODE_PATH}" ./
}

#4. 安装python3
function install_python () {
    echo "$1" # arguments are accessible through $1, $2,...
}

#5. 虚拟环境
function install_virtualenv () {
    echo "$1" # arguments are accessible through $1, $2,...
}

#6. 安装包
function install_extend () {
    echo "$1" # arguments are accessible through $1, $2,...
}

#7. gunicorn
function install_gunicorn () {
    echo "$1" # arguments are accessible through $1, $2,...
}

#8. nginx
function install_config_nginx () {
    echo "$1" # arguments are accessible through $1, $2,...
}

#9. 进程管理
function check_process () {
    echo "$1" # arguments are accessible through $1, $2,...
}

#10. 测试
```

