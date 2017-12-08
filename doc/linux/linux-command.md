问题排查常用linux命令
tail head:
tail -f  #监听写入
tail -100  #查看末尾100行
head -100  #查看开头100行
tail -100f

grep:
grep keyWord foo.txt #在文件中查找keyWord行， 可以跟多个文件
grep keyWord ./  -r  #在目录下查找包含keyWord的行，目录中查找需要 -r 参数递归查找的意思
grep keyWord ./*.vm  #支持通配符
另：最后可以跟 -C 3 参数，表示展示匹配行的上下3行

awk：
awk是一个强大的文本分析工具,awk把文件逐行读入，以空格和tab为默认分隔符将每行切片，切开的部分再进行各种分析处理。awk是以文件的一行为处理单位的，读完一行，将记录按照指定的分隔符划分域，$0表示所有域，$1表示第一个域，$n表示第n个域。
通用使用方法：awk -F: '[pattern]{action}' filename #-F指定分隔符，pattern支持正则，可选，匹配相应行；action是awk内置动作，常用的print
awk '{print $1}' f.md  #打印第一个域的内容
awk '{print NR, FNR, FILENAME, NF}' f.md  #几个内建变量：NR行数，FNR文件行数，处理多个文件使用，NF某一行域个数
--匹配
可以直接使用== > <等进行比较
awk '$5 == "test" {print $0}' f.md #第五列等于test
awk '/key/' f.md #匹配含有key关键字的行，默认打印
awk '!/key/' f.md #匹配不含邮key关键字的行
awk '/key/ && /word/' f.md #匹配含有key和word的行
awk '$5 ~ /key/' f.md #
--高级用法
拆分文件：
netstat | awk 'NR!=1{print > $6}' #根据第6列拆分文件
使用if elseif
netstat | awk 'NR!=1{if($6 ~ /list/) print > 1.txt; else if($6 ~ /test/) print > 2.txt else print > 3.txt}'
使用for
ps aux | awk 'NR!=1{a[$1]+=$6;} END {for(i in a) print i ", " a[i];}'
脚本
awk -f script.awk f.md #对f.md执行脚本，脚本内容与引号内相同
BEGIN{处理文本前执行}
END{处理完所有行后执行}
{匹配完每一行后执行}

find:
find 多个目录 -name *.log #在目录下查找文件
find 多个目录 -iname *.log #不区分大小写

bc:
计算命令，直接输入bc进入交互模式，或使用管道 echo "1+1" | bc 用来计算
默认10进制，通过 ibase=2;obase=16;转换输入和输出进制

top:
--top第一行：16:38:41 up 1324 days, 23:24,  1 user,  load average: 0.29, 0.26, 0.29
与uptime命令类似
分别是：系统时间，机器运行时间，登录用户和平均负载
使用who命令查看登录了哪些用户,或者使用w命令可以查看更多信息，最后一行可以查看此用户正在执行的命令
load average：1min，5min，15min到现在的平均值
load 表示cpu处理队列长度。单核cpu的load满载为1，表示一个处理时刻cpu处理一个任务，单核load是可以大于1的，说明一个处理时刻，cpu要处理的任务数大于1，此时在超负荷运行。同理4核cpu满载为4
--top第二行：Tasks: 139 total,   1 running, 138 sleeping,   0 stopped,   0 zombie
stop进程是中止执行，等待调度信号。zombie进程是进程已经终止，等待父进程收集统计信息
--top第三行：Cpu(s):  3.5%us,  3.4%sy,  0.0%ni, 92.3%id,  0.3%wa,  0.3%hi,  0.3%si,  0.0%st
在top运行时按【1】可以查看每个cpu的运行情况
us指用户空间占用cpu时间的百分比，sy指系统空间占用cpu时间的百分比；进程执行用户自己代码时，处于用户运行态，耗费cpu时间计入us，进程执行系统调用，处于内核运行态，耗费cpu时间计入sy。
ni即nice，用户进程空间改变过优先级的进程占用cpu百分比。ni对每个进程是固定值，负值高优先级，获得更多的cpu时间片。
wa等待磁盘读写消耗的cpu时间，cpu需要等待磁盘写入完成才能进行下一步操作。
hi si是硬中断 软中断耗费的cpu时间
id：空闲时间占比
st：steel只有主机是虚拟机时才有，表示虚拟机从宿主机获取cpu时间片的百分比
--top第四，五行：
Mem:   8190892k total,  7957612k used,   233280k free,   228860k buffers
Swap:        0k total,        0k used,        0k free,  4592504k cached
mem是真实内存，swap是交换内存
total=used+free
cache和buffer是缓存内存，程序使用内存时就会释放。
--进程信息区：pid进程id，uid用户id，user用户名，tty终端名或？，pr优先级，cpu占用百分比，mem物理内存百分比，virt虚拟内存，time使用线程时间，s状态，command命令
top -H -p <pid> #-p指定pid，-H展示所有线程(mac 没有该命令)
一般load average低于cpu*0.7算正常，查看有多少个cpu（linux下）：cat /proc/cpuinfo | grep "cpu cores" | uniq

su和sudo：
su <user> #执行这个命令需要输入user的password，之后以user定义的用户运行shell
sudo su <user> #执行这个命令输入你自己的密码，之后检查/etc/sudoers中是否你有sudo权限，之后也是以user定义的用户运行shell

sudo -u admin 与 sudo su admin拥有的执行权限似乎是不同的。譬如sudo -u admin sh -x preload.sh 不可以运行，但是sudo su admin 然后 sh -x admin就可以执行


