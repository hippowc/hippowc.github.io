查询机器运行状态的那些命令
uptime
查看机器负载

top
查看各个进程的资源占用状况

jstack pid
查看jvm当前的thread dump的，可以看到当前jvm里面的线程状况，对于查找blocked线程比较有用

jps -lmv
JVM机进程状况工具，用来查看基于HotSpot JVM里面所有进程的具体状态，包括进程ID，进程启动的路径等等。与linux上的ps类似，用来显示本地有权限的java进程，可以查看本地运行着几个java程序，并显示他们的进程号，使用jsp时不需要传递进程号作为参数

-m 输出传递给main方法的参数，如果是内嵌的JVM则输出为null
-l 输出应用程序主类的完整包名，或者是应用程序JAR文件的完整路径
-v 输出传给JVM的参数
jstat
查看Jvm的堆栈信息，能够查看eden,survivor,old,perm等heap得capacity,utility信息，对于查看系统是不是有内存泄露以及参数设置是否合理有意义

1.jstat -gc pid

可以显示gc的信息，查看gc的次数及时间
其中最后五项，分别是young gc的次数，young gc的时间，full gc的次数，full gc的时间，gc的总时间
2.jstat -gccapacity pid

可以显示VM内存中三代(young,old,perm)对象的使用和占用大小，如：PGCMN显示的是最小perm的内存使用量，PGCMX显示的是perm的内存最大使用量，PGC是当前新生成的perm内存占用量，PC是但前perm内存占用量。其他的可以根据这个类推， OC是old内纯的占用量。
3.jstat -gcutil pid

统计gc信息
4.jstat -gcnew pid

年轻代对象的信息
5.jstat -gcnewcapacity pid

年轻代对象的信息及占用量
6.jstat -gcold pid

old代对象的信息
7.jstat -gcoldcapacity pid

old代对象的信息及其占用量
8.jstat -gcpermcapacity pid

perm对象的信息及其占用量
9.jstat -class pid

显示加载class的数量，及所占空间等信息
10.jstat -compiler pid

显示VM实时编译的数量等信息
11.jstat -printcompilation pid

当前VM执行的信息
jmap
查看jvm当前的heap dump的，可以看出当前jvm中各种对象的数据，所占空间等等，尤其值得一提的是这个命令可以导出一份binary heap dump的bin文件，这个文件能够直接用Eclipse Memory Anayliser来分析，并找出潜在的内存泄露的地方

常用参数说明：
-dump:[live,]format=b,file= 使用二进制形式输出jvm的heap内容到文件中， live子选项是可选的，假如指定live选项,那么只输出活的对象到文件.
-histo[:live] 打印每个class的实例数目,内存占用,类全名信息. VM的内部类名字开头会加上前缀”*”. 如果live子参数加上后,只统计活的对象数量.
-F 强迫.在pid没有相应的时候使用-dump或者-histo参数. 在这个模式下,live子参数无效.
例如：以二进制形式输入当前堆内存映像到文件data.hprof中

/Users/coldface>jmap -dump:live,format=b,file=data1.hprof 4304
Dumping heap to /Users/coldface/data1.hprof ...
Heap dump file created
jhat：JVM堆转储快照分析工具
用于对JAVA heap进行离线分析的工具，他可以对不同虚拟机中导出的heap信息文件进行分析，如LINUX上导出的文件可以拿到WINDOWS上进行分析，可以查找诸如内存方面的问题。
命令格式：jhat dumpfile(jmap生成的文件)

例如：分析jmap导出的内存映像

/Users/coldface>jhat data1.hprof
Reading from data1.hprof...
Dump file created Sat Oct 17 10:27:25 CST 2015
Snapshot read, resolving...
Resolving 879257 objects...
Chasing references, expect 175 dots............................................
...............................................................................
..................................................
Eliminating duplicate references...............................................
...............................................................................
...............................................
Snapshot resolved.
Started HTTP server on port 8090
Server is ready.
执行成功后，访问 http://localhost:8090 即可查看内存信息

例子
线上机器load突然飙升，无法响应正常请求，需要查询一起load飙升的线程究竟在做什么？哪些线程霸占了cpu？
可以通过top和jstack命令来查看，具体步骤如下：

1.执行top命令，默认是进程视图，其中PID是进程号

2.由于我们要看线程，在终端按下H键或者shift+h，top视图会切换到线程视图，其中PID是线程号
--解释下，线程号也就是轻量级进程，也就是java中的Thread

3.打开终端2，使用jstack命令输入这一时刻的线程栈，保存到文件。
jstack保存的是当前时刻这个pid所有的存活线程的信息

4.由于保存的文件记录的线程ID是16进制，需要将top命名展示的线程号转换成16进制，以12300为例，在linux下输入命令：printf 0x%x 12300，得到12300的十六进制为0x300c

5.在保存的文件中搜索0x300c关键字

一些术语的中文解释
S0C：年轻代中第一个survivor（幸存区）的容量 (字节)
S1C：年轻代中第二个survivor（幸存区）的容量 (字节)
S0U：年轻代中第一个survivor（幸存区）目前已使用空间 (字节)
S1U：年轻代中第二个survivor（幸存区）目前已使用空间 (字节)
EC：年轻代中Eden（伊甸园）的容量 (字节)
EU：年轻代中Eden（伊甸园）目前已使用空间 (字节)
OC：Old代的容量 (字节)
OU：Old代目前已使用空间 (字节)
PC：Perm(持久代)的容量 (字节)
PU：Perm(持久代)目前已使用空间 (字节)
YGC：从应用程序启动到采样时年轻代中gc次数
YGCT：从应用程序启动到采样时年轻代中gc所用时间(s)
FGC：从应用程序启动到采样时old代(全gc)gc次数
FGCT：从应用程序启动到采样时old代(全gc)gc所用时间(s)
GCT：从应用程序启动到采样时gc用的总时间(s)
NGCMN：年轻代(young)中初始化(最小)的大小 (字节)
NGCMX：年轻代(young)的最大容量 (字节)
NGC：年轻代(young)中当前的容量 (字节)
OGCMN：old代中初始化(最小)的大小 (字节)
OGCMX：old代的最大容量 (字节)
OGC：old代当前新生成的容量 (字节)
PGCMN：perm代中初始化(最小)的大小 (字节)
PGCMX：perm代的最大容量 (字节)
PGC：perm代当前新生成的容量 (字节)
S0：年轻代中第一个survivor（幸存区）已使用的占当前容量百分比
S1：年轻代中第二个survivor（幸存区）已使用的占当前容量百分比
E：年轻代中Eden（伊甸园）已使用的占当前容量百分比
O：old代已使用的占当前容量百分比
P：perm代已使用的占当前容量百分比
S0CMX：年轻代中第一个survivor（幸存区）的最大容量 (字节)
S1CMX ：年轻代中第二个survivor（幸存区）的最大容量 (字节)
ECMX：年轻代中Eden（伊甸园）的最大容量 (字节)
DSS：当前需要survivor（幸存区）的容量 (字节)（Eden区已满）
TT： 持有次数限制
MTT ： 最大持有次数限制
