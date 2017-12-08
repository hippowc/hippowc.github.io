讨论qps，每秒钟的请求数量。可能与编程语言、编程模型（nio，coroutine有关）、业务逻辑复杂度、数据结构和算法、系统等等都有点关系。
1、单线程rt，response time，线程cpu时间+线程等待时间。对于web用户来讲，客户感知的rt=服务器的rt和网络rt。对于优化网络rt有增加cdn，专线等等方式。
主要讨论服务器rt。
2、单线程的qps：每秒的请求数，数值上等于 1s／rt
3、最佳线程数：以一个例子来看：假设一个服务器的rt是199ms，thread wait是180ms，thread cpu是19ms，服务器的qps就是 1000ms／199
刚刚提到会有thread wait等待时间，譬如在等待io时，cpu在得到返回结果期间是啥也没做的，浪费。由此可以采用多线程，在cpu这段等待时间内，去处理其他事情。cpu
线程上下文切换可能需要1ms，那么处理时间变为20ms，在等待的180ms中，可以切换出去做9个这样的请求。所以这个时候开启10个线程能够充分利用cpu。这个是单核情况。
n核就乘以n就可以。实际情况中cpu利用率不是100%，那么最佳线程数大概公式=（rt／cpu时间+线程切换时间）*核*利用率
4、最大qps：最佳线程数*单线程qps
5、cpu时间：一次请求中所有环节耗费cpu时间之和。cpu时间主要由这两个因素决定：数据结构和算法
6、同步模型和异步模型：
同步模型：一个线程服务一个client，在socket读取以及写入等等io时，线程处于block状态，会有很多的wait time。优点：编程简单，符合人类思维方式。对于cpu密集型应用，并发数小的情况适合使用。缺点：如果wait time很长，io密集型的应用，将会需要很多线程来达到最优qps，但是线程多了之后，线程切换成本会变高，达不到最优。
异步模型：特点是：可以支持大量连接，业务执行过程中没有io阻塞，基于nio和aio实现。异步模型依赖队列，需要大量的回调函数。代码复杂度比较高。优点：特定场景下更高的qps，最佳线程数少而固定。缺点：编程时较为复杂

java性能优化分层：
应用层：理解代码逻辑，通过java线程栈定位有问题代码行；
数据库层：分析sql，定位死锁；
框架层：懂源代码，理解框架机制
jvm层：了解gc类型和工作机制，了解jvm参数

使用性能诊断工具
os层面和java应用层面

os诊断：cpu，memory，io
cpu诊断：
关注点：平均负载，cpu使用率，上下文切换次数
命令：top
top #大概两个展示区：统计信息区和进程信息区
统计信息区：整体信息，cpu信息，内存信息；关注点：load average：1min，5min，15min到现在的平均值
进程信息区：pid进程id，uid用户id，user用户名，tty终端名或？，pr优先级，cpu占用百分比，mem物理内存百分比，virt虚拟内存，time使用线程时间，s状态，command命令
top -H -p <pid> #-p指定pid，-H展示所有线程(mac 没有该命令)
一般load average低于cpu*0.7算正常，查看有多少个cpu（linux下）：cat /proc/cpuinfo | grep "cpu cores" | uniq

命令：vmstat
查看cpu上下文切换次数，也就是执行任务切换次数

java应用层诊断：
命令：jstack
配合top使用，通过top -H -p <pid>定位java进程和线程，使用jstack -l pid 导出线程栈。线程栈是瞬态的，5s导出一次，导出三次比较。
jstack打印出来的命令第一行："main" #1 prio=5 os_prio=31 tid=0x00007ffe3d801800 nid=0x1c03 in Object.wait() [0x000070000c20a000]
含义依次时：线程名，优先级信息，tid java thread id，nid native线程id
top定位的java线程pid转为16进制，可以得到jstack命令中的nid，其中nid是jstack命令中表示线程的id。
使用jstack，主要关注几种线程状态，当jstack命令打印线程栈时，会有一行: java.lang.thread.state: xxx，这个地方就是线程状态。关注这么几种状态：
blocked, waiting, timed_waiting等，线程这几种状态详细可以参考java.lang.thread.state这个枚举类，里面讲线程出现这个状态的原因也进行了说明。其中
blocked：说明这个线程正在等待monitor lock，从而进入一个同步块或同步方法。或者是等待后重新进入
waiting：当调用者这几个方法：Object.wait() Thread.join(),LockSupport.park 会进入waiting状态，需要等待其他线程进行某种操作才可以恢复
timed_waiting:这个线程会等待一定时间后执行，一般调用这几个方法：Thread.sleep(), Object.wait(long), Thread.join(long), LockSupport.parkNano()等方法

一种排查思路：（mac没有查看线程的命令）
使用top或者jps命令获取load较高的进程
使用top -p <pid> -H 或者ps -mp <pid> -o THREAD,tid 获取该进程的所有线程
使用 printf "%x\n" tid 或者 echo "obase=16; <pid>" | bc 将线程转换为16进制（jstatck中tid是16进制的）
使用 jstack <pid> | grep <tid> -A30 定位到该栈状态
定位代码位置进行排查
