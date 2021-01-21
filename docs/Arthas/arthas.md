# Arthas

## 1.	Arthas介绍

> - 对于jvm的常用场景比如Cpu持续占用率高、死锁和内存泄漏（OOM）等的场景的排查都是用的Jdk自带的小工具(top、jps、jstack和jmap)来排查问题，效率低且复杂，比如排查cpu占用率高的问题就需要top->top->jps->printf->jstack等一系列的操作，而Arthas可以简化这些操作，一个thread命令即可解决。
> - Arthas是阿里巴巴开源的一个工具，可以检测线程，内存，堆栈，GC，Runtime等信息
> - 帮助文档：https://arthas.aliyun.com/doc/commands.html

## 2.	安装

>- 直接下载一个启动的jar包然后用java -jar的方式启动
>
>  ```shell
>  # 直接下载到你想要的目录
>  curl -O https://arthas.gitee.io/arthas-boot.jar
>  ```

## 3.	启动

> - java -jar 直接启动后选定的要监测的进程号id即可启动成功
>
>   ```shell
>   java -jar arthas-boot.jar 
>   ```

## 4.	相关命令

### 4.1	基础命令

| 命令             | 功能                                 | 备注                   |
| ---------------- | ------------------------------------ | ---------------------- |
| dashboard        | 显示JVM中内存的情况，JVM中环境信息   |                        |
| thread           | 显示当前进程中所有线程信息           |                        |
| jad              | 反编译指定的类或方法得到java的源代码   | 将出现ClassLoader(AppClassLoader，ExtClassLoader)，定位到类对应的jar包以及反编译得到的源代码。 |
| watch            | 监视某个方法的执行情况，监视了返回值 | 举例：watch demo.MathGame primeFactors retuenobj（监控demo.MathGame类下的方法primeFactors的返回值）。 |
| quit，exit，stop | 退出或停止arthas                     |                        |
| mc | 在内存中将源代码编译成字节码文件即.classs文件 | |
| redefine | 将字节码文件重新加载到内存中执行 | jad+mc+redefine在停止应用的情况下，更改部分代码进行修改验证。 |
| help | 显示所有arthas命令，每个命令都可以使用-h的参数，显示它的参数信息 |  |
| cat | 显示文本文件内容 |  |
| grep | 对内容进行过滤，只显示关心的行 |  |
| pwd | 显示当前的工作路径 |  |
| cls | 清楚屏幕 |  |
| session | 显示当前连接的会话ID | 举例：<br/>JAVA_PID    6212<br/>SESSION_ID  6fbef6ef-dbab-4ee8-99b5-545f1a6d5ec4 |
| reset | 重置arthas增强的类 |  |
| version | 显示当前arthas的版本号 |  |
| keymap | 显示所有的快捷键 |  |
|  |  |  |

```shell
# jad反编译命令
$ jad demo.MathGame
 
ClassLoader:
+-sun.misc.Launcher$AppClassLoader@3d4eac69
  +-sun.misc.Launcher$ExtClassLoader@66350f69
 
Location:
/tmp/arthas-demo.jar
 
/*
 * Decompiled with CFR 0_132.
 */
package demo;
 
import java.io.PrintStream;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.Random;
import java.util.concurrent.TimeUnit;
 
public class MathGame {
    private static Random random = new Random();
    private int illegalArgumentCount = 0;
 
    public static void main(String[] args) throws InterruptedException {
        MathGame game = new MathGame();
        do {
            game.run();
            TimeUnit.SECONDS.sleep(1L);
        } while (true);
    }
 
    public void run() throws InterruptedException {
        try {
            int number = random.nextInt();
            List<Integer> primeFactors = this.primeFactors(number);
            MathGame.print(number, primeFactors);
        }
        catch (Exception e) {
            System.out.println(String.format("illegalArgumentCount:%3d, ", this.illegalArgumentCount) + e.getMessage());
        }
    }
 
    public static void print(int number, List<Integer> primeFactors) {
        StringBuffer sb = new StringBuffer("" + number + "=");
        Iterator<Integer> iterator = primeFactors.iterator();
        while (iterator.hasNext()) {
            int factor = iterator.next();
            sb.append(factor).append('*');
        }
        if (sb.charAt(sb.length() - 1) == '*') {
            sb.deleteCharAt(sb.length() - 1);
        }
        System.out.println(sb);
    }
 
    public List<Integer> primeFactors(int number) {
        if (number < 2) {
            ++this.illegalArgumentCount;
            throw new IllegalArgumentException("number is: " + number + ", need >= 2");
        }
        ArrayList<Integer> result = new ArrayList<Integer>();
        int i = 2;
        while (i <= number) {
            if (number % i == 0) {
                result.add(i);
                number /= i;
                i = 2;
                continue;
            }
            ++i;
        }
        return result;
    }
}
 
Affect(row-cnt:1) cost in 970 ms.
```



### 4.2	jvm相关命令

| jvm相关命令 | 功能                                                         |
| ----------- | ------------------------------------------------------------ |
| dashboard   | 仪表板，可以显示：线程，内存，堆栈，gc，runtime等信息        |
| thread      | 显示线程的堆栈                                               |
| jvm         | 显示java虚拟机信息                                           |
| sysprop     | 查看和修改JVM的系统属性                                      |
| sysenv      | 查看JVM的环境变量                                            |
| vmoption    | 显示jvm中选项信息                                            |
| getstatic   | 获取类中静态成员变量，仅能获取这里的数据private static Random random = new Random(); |
| ognl        | 执行一条ognl表达式，对象图导航语言，ognl '@demo.MathGame@random'； |

### 4.3	class/classsloader相关命令

| 类，类加载相关的命令 | 说明                                                         |
| -------------------- | ------------------------------------------------------------ |
| sc                   | Search Class查看运行中的类信息                               |
| sm                   | Search Method查看类中方法的信息                              |
| jad                  | 反编译字节码为源文代码                                       |
| mc                   | Memory Compile将源代码编译成字节码文件                       |
| redefine             | 将编译好的字节码文件加载到JVM中运行                          |
| dump                 | dump已加载类的byte code到特定目录                            |
| Classloader          | 1.classloader命令将JVM中所有的classloader的信息统计出来，并可以展示继承树，urls等；<br>2.可以让指定的`classloader`去getResources，打印出所有查到的resources的url，对于ResourceNotFoundException异常比较有用。<br>3.出现的效果是：名字，实例数量，所有加载类的总数 |
| monitor              | 监控`Java`方法执行的                                         |

### 4.4	monitor/watch/trace/stack方法

>1. 请注意，这些命令，都通过字节码增强技术来实现的，会在指定类的方法中插入一些切面来实现数据统计和观测，因此在线上、预发使用时，请尽量明确需要观测的类，方法以及条件，诊断结束要执行或将增强过的类执行命令。
>2. monitor(方法执行监控)，watch(方法执行数据观测)，trace(方法内部调用路径，并输出方法路径上的每个节点上耗时)，stack(输出当前方法被调用的调用路径)，tt(方法执行数据的时空隧道，记录下制定方法每次调用的入参和返回信息，并能对这些不同的时间下调用进行观测)

#### 4.4.1	monitor

>1. monitor命令的作用：用来监视一个时间段中指定方法的执行次数、成功次数、失败次数、耗时等信息

| 监控项    | 说明                   |
| --------- | ---------------------- |
| timestamp | 时间戳                 |
| class     | java类                 |
| method    | 方法（构造方法、普通） |
| total     | 调用次数               |
| success   | 成功次数               |
| fail      | 失败次数               |
| rt        | 平均rt                 |
| fail-rate | 失败率                 |

>1. 这里面`fail`的标准是抛出异常，其中`rt`这个参数比较有意义，可以查看一下方法调用的耗时情况，比如不同参数场景下的方法耗时，这个统计仅供参考。原因下面会讲。
>2. 方法调用比较简单，只有一个参数`-c`表示统计周期的，默认是`120s`。文档中表示执行只命令会导致`JVM`运行改方法变慢，这里我做了实验，大概会提高`50%`的`rt`，结束`arthas`之后恢复原来的性能。

#### 4.4.2	watch

> 1. watch作用：观察到的范围为：返回值，抛出异常，入参

| 参数              | 说明                                 |
| ----------------- | ------------------------------------ |
| class-pattern     | 类名表达匹配                         |
| method-pattern    | 方法名表达式匹配                     |
| condition-express | 条件表达式，使用ognl表达式           |
| [E]               | 开启正则表达式匹配，默认是通配符匹配 |
| [n:]              | 设置命令执行次数                     |
| #cost             | 方法执行耗时，单位是毫秒             |
| [b]               | begin监视方法执行前的情况            |
| [e]               | exception监视出现异常的情况          |
| [s]               | success执行成功情况                  |
| [f]               | finish执行完毕的情况，包含成功或失败 |
| target            | 调用方法的对象实例                   |
| params            | 方法参数                             |
| returnObj         | 返回值，如果有的话                   |
| returnExp         | 异常，如果有的话                     |

```shell
# 例子1，获取方法执行前和后的参数，对象实例，返回值，展开的层级为2
watch demo.MathGame primeFactors "{params,target,returnObj}" -x 2 -b -s -n 2

#例子2,获取方法小于0的参数，对象实例
watch demo.MathGame primeFactors "{params[0],target}" "params[0]<0"
```

#### 4.4.3	trace

> 1. trace命令的作用：trace可以发现某个接口的耗时，输出方法内部每个节点的耗时
> 2. trace命令只能trace匹配到的函数里的子调用，并不会向下trace多层。因为trace是代价比较贵的，多层trace可能会导致最终要trace的类和函数非常多
> 3. trace指定类的方法

#### 4.4.4	stack

> 1. stack作用：跟踪方法的堆栈信息

#### 4.4.5	tt

> 1. tt-time tunner时间隧道，记录下指定方法每次调用的入参和返回信息，并能对这些不同时间下调用的信息进行观测
> 2. 记录下当时方法的所有入参和返回值，抛出的异常

| 参数      | 说明                             |
| --------- | -------------------------------- |
| -t        | 记录某个方法在一个时间段中的调用 |
| -l        | 显示所有已经记录的列表           |
| -n 次数   | 只记录多少次                     |
| -s 表达式 | 搜索表达式                       |
| -i 索引号 | 查看指定索引号的详细调用信息     |

### 4.5	全局开关options

| 名称                | 默认值 | 描述                                                         |
| ------------------- | ------ | ------------------------------------------------------------ |
| unsafe              | false  | 是否支持对系统级别的类进行增强，打开该开关可能导致把JVM搞挂，请慎重选择！ |
| dump                | false  | 是否支持被增强了的类dump到外部文件中，如果打开开关，class文件会被dump到`/${application working dir}/arthas-class-dump/`目录下，具体位置详见控制台输出 |
| batch-re-transform  | true   | 是否支持批量对匹配到的类执行retransform操作                  |
| json-format         | false  | 是否支持json化的输出                                         |
| disable-sub-class   | false  | 是否禁用子类匹配，默认在匹配目标类的时候会默认匹配到其子类，如果想精确匹配，可以关闭此开关 |
| debug-for-asm       | false  | 打印ASM相关的调试信息                                        |
| save-result         | false  | 是否打开执行结果存日志功能，打开之后所有命令的运行结果都将保存到`~/logs/arthas-cache/result.log`中 |
| job-timeout         | 1d     | 异步后台任务的默认超时时间，超过这个时间，任务自动停止；比如设置 1d, 2h, 3m, 25s，分别代表天、小时、分、秒 |
| print-parent-fields | true   | 是否打印在parent class里的filed                              |

### 4.6	火焰图

> 1. 使用profile生成火焰图
>
> 2. `profiler` 命令支持生成应用热点的火焰图。本质上是通过不断的采样，然后把收集到的采样结果生成火焰图
>
> 3.  profiler` 命令基本运行结构是 `profiler action [actionArg]
> 

| 参数名称（一下的命令仅使用于linux/mac系统上） | 参数说明                                                     |
| --------------------------------------------- | ------------------------------------------------------------ |
| profiler start                                | 1. 启动profile，默认情况下，生成cpu的火焰图<br>2. 通过--event参数来指定采样的指标如profiler start --event cpu |
| profiler list                                 | 显示所有支持的事件                                           |
| profiler getSamples                           | 获取已采集的sample的数量                                     |
| profiler status                               | 查看profile的状态，执行时间，显示的结果含有监控的指标是什么且已监视了多久的时间 |
| profiler stop                                 | 1. 停止profiler,生成火焰图的结果，指定输出目录和输出格式，svg和html<br>2. 格式profiler stop --format html或者profiler stop -format svg |

> 1. y轴表示调用栈，每一层都是一个函数，调用栈越深，火焰就越高，顶部就是正在执行的函数，下方就是他的父函数
> 2. x轴表示抽样数，如果一个函数在x轴占据的宽度越宽，就表示它被抽到的次数多，即执行的时间长，注意，x轴不代表时间，而是所有的调用栈合并后，按字母顺序排列的
> 3. 火焰图就是看顶层的哪个函数占据的宽度最大，只要有平顶，就表示该函数可能存在性能问题，颜色没有特俗含义，因为火焰图表示的是cpu的繁忙程度，所以一般选择暖色调

### 4.7	总结

| 命令        | 说明                                                       |
| ----------- | ---------------------------------------------------------- |
| dump        | 将已加载的字节码文件保存到特定的目录下                     |
| classloader | 获取类加载器的信息                                         |
| monitor     | 监控指定类中方法的执行情况                                 |
| watch       | 观察到指定方法的调用情况                                   |
| trace       | 对方法内部调用路径进行追踪，并输出方法路径上每个节点上耗时 |
| stack       | 输出当前方法被调用的路径                                   |
| tt          | 记录指定方法每次调用的入参和返回信息                       |
| options     | 全局开关                                                   |
| profiler    | 生成火焰图=                                                |

## 5.	卸载

>- 直接删除2个文件夹：.arthas和logs这两个文件夹。

```shell
# .arthas的目录地址
C:\Users\Administrator\.arthas\lib\3.4.6\arthas

# logs的目录地址
C:\Users\Administrator\logs
```

## 6.	Arthas的应用

### 6.1	Cpu占用过高

>1. 用thread命令列出线程的信息，这个命令会把所有线程按照cpu占用率从高到低列出来，如果线程太多，可以通过-n参数指定输出的行数；
>2. thread加线程id输出改线程的栈信息；
>3. 查看输出的栈信息，定位问题。

### 6.2	死锁

>1. thread -b查看输出的信息，定位死锁。

### 6.3	内存泄漏

>1. 用dashboard命令来动态查看内存情况（如果内容使用率在不断上升，而且gc后也不下降，后面还发现gc越来越频繁，很可能就是内存泄漏了。）
>
>2. heapdump命令把内存快照dump出来，作用和jmap工具一样（jmap可以把指定java进程的内存快照dump出来，效果和第一种处理办法一样，不同的是它不用等OOM就可以做到，而且dump出来的快照也会小很多。）
>
>   ```shell
>   # heapdump的方式
>   heapdump --live /root/jvm.hprof
>   # jmap的方式
>   jmap -dump:live,format=b,file=heap.bin 24836
>   ```
>
>3. MAT插件分析（eclipse的MAT插件步骤File->Open Heap Dump...）选择Leak Suspects