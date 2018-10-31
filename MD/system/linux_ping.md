# Linux查看网络 内存 日志命令

## 网络

#### **1. hostname**

hostname 没有选项，显示主机名字

**hostname –d** 显示机器所属域名
**hostname –f** 显示完整的主机名和域名
**hostname –i** 显示当前机器的ip地址

#### **2. ping**

ping 将数据包发向用户指定地址。当包被接收。目标机器发送返回数据包. ping 主要有两个作用

1. 用来确认网络连接是畅通的。

2. 用来查看连接的速度信息。

如果你 ping www.yahoo.com 它将返回它的ip地址 。你可以通过 ctrl+C 来停止命令。

#### **3. ifconfig**

查看用户网络配置。它显示当前网络设备配置。对于需要接收或者发送数据错误查找，这个工具极为好用。

#### **4. iwconfig**

iwconfig 工具与 ifconfig 和ethtool类似。是用于无线网卡的 . 你可以用他查看设置基本的Wi-Fi 网络信息,例如 SSID, channel和encryption.还有其他很多配置你也可以查看和修改，, 包括 接收灵敏度, RTS/CTS, 发送数据包的分片大小,以及无线网卡的重传机制

#### **5. nslookup**

nslookup 这个命令在 有ip地址时，可以用这个命令来显示主机名，可以找到给定域名的所有ip地址。而你必须连接到互联网才能使用这个命令

例子. nslookup blogger.com

你也可以使用 nslookup 从ip获得主机名或从主机名获得ip。

#### **6. traceroute**

一个方便的工具。可用来查看数据包在提交到远程系统或者网站时候所经过的路由器的IP地址、跳数和响应时间。同样你必须链接到互联网才能使用这个命令

#### **7. finger**

查看用户信息。显示用户的登录名字、真实名字以及登录终端的名字和登录权限。这是unix一个很老的命令，现在已很少使用了

#### **8. telnet**

通过telnet协议连接目标主机，如果telnet连接可以在任一端口上完成即代表着两台主机间的连接良好。

telnet hostname port - 使用指定的端口telnet主机名。这通常用来测试主机是否在线或者网络是否正常。

#### **9. ethtool**

ethtool允许你查看和更改网卡的许多设置（不包括Wi-Fi网卡）。你可以管理许多高级设置，包括tx/rx、校验及网络唤醒功能。下面是一些你可能感兴趣的基本命令：

显示一个特定网卡的驱动信息，检查软件兼容性时尤其有用。

**ethtool -i**

启动一个适配器的指定行为，比如让适配器的LED灯闪烁，以帮助你在多个适配器或接口中标识接口名称：

**ethtool -p**

显示网络统计信息：

**ethtool -s**

设置适配器的连接速度，单位是Mbps：

**ethtool speed <10|100|1000>**

#### **10. netstat**

发现主机连接最有用最通用的Linux命令。你可以使用"netstat -g"查询该主机订阅的所有多播组（网络）

**netstat -nap | grep port** 将会显示使用该端口的应用程序的进程id
**netstat -a  or netstat –all** 将会显示包括TCP和UDP的所有连接  
**netstat --tcp  or netstat –t** 将会显示TCP连接
**netstat --udp or netstat –u** 将会显示UDP连接
**netstat -g** 将会显示该主机订阅的所有多播网络。

## 内存

#### top命令：

```
top - 02:53:32 up 16 days,  6:34, 17 users,  load average: 0.24, 0.21, 0.24
Tasks: 481 total,   3 running, 474 sleeping,   0 stopped,   4 zombie
Cpu(s): 10.3%us,  1.8%sy,  0.0%ni, 86.6%id,  0.5%wa,  0.2%hi,  0.6%si,  0.0%st
Mem:   4042764k total,  4001096k used,    41668k free,   383536k buffers
Swap:  2104472k total,     7900k used,  2096572k free,  1557040k cached

PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
32497 jacky     20   0  669m 222m  31m R   10  5.6       29:27.62 firefox
4788 yiuwing   20   0  257m  18m  13m S    5  0.5          5:42.44 konsole
5657 Liuxiaof  20   0  585m 159m  30m S    4  4.0          5:25.06 firefox
4455 xiefc      20   0  542m  124m  30m R    4  3.1         7:23.03 firefox
```

```
PID：进程的ID
USER：进程所有者
PR：进程的优先级别，越小越优先被执行
NInice：值
VIRT：进程占用的虚拟内存
RES：进程占用的物理内存
SHR：进程使用的共享内存
S：进程的状态。S表示休眠，R表示正在运行，Z表示僵死状态，N表示该进程优先值为负数
%CPU：进程占用CPU的使用率
%MEM：进程使用的物理内存和总内存的百分比
TIME+：该进程启动后占用的总的CPU时间，即占用CPU使用时间的累加值。
COMMAND：进程启动命令名称
```

### free命令

然而有时候，`top` 命令可能不能满足你的需求。你可能只需要查看系统的可用和已用内存。对此，Linux 还有 `free` 命令。`free` 命令显示：

- 可用和已使用的物理内存总量
- 系统中交换内存的总量
- 内核使用的缓冲区和缓存

## 日志

#### tail:  

-n  是显示行号；相当于nl命令；例子如下：

```
tail -100f test.log      实时监控100行日志
tail  -n  10  test.log   查询日志尾部最后10行的日志;
tail -n +10 test.log    查询10行之后的所有日志;
```

#### head:  

跟tail是相反的，tail是看后多少行日志；例子如下：

```
head -n 10  test.log   查询日志文件中的头10行日志;
head -n -10  test.log   查询日志文件除了最后10行的其他所有日志;
```

#### cat：

应用场景一：按行号查看---过滤出关键字附近的日志

```
1）cat -n test.log |grep "debug"  得到关键日志的行号

2）cat -n test.log |tail -n +92|head -n 20  选择关键字所在的中间一行. 然后查看这个关键字前10行和后10行的日志:
	tail -n +92表示查询92行之后的日志

    head -n 20 则表示在前面的查询结果里再查前20条记录
```

应用场景二：根据日期查询日志

```
sed -n '/2014-12-17 16:17:20/,/2014-12-17 16:17:36/p'  test.log

特别说明:上面的两个日期必须是日志中打印出来的日志,否则无效；

先 grep '2014-12-17 16:17:20' test.log 来确定日志中是否有该 时间点
```

应用场景三：日志内容特别多，打印在屏幕上不方便查看	

```
(1)使用more和less命令,

如： cat -n test.log |grep "debug" |more     这样就分页打印了,通过点击空格键翻页

(2)使用 >xxx.txt 将其保存到文件中,到时可以拉下这个文件分析

如：cat -n test.log |grep "debug"  >debug.txt
```
















