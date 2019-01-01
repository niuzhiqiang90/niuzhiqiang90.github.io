---
title: linux系统监控基本命令
date: 2017-03-25 17:09:57
categories: linux
tags: linux
---

## 1. nmon软件

### 1.1 运行nmon软件

```
:~$ nmon 
```

```
┌nmon─14g──────[H for help]───Hostname=localhost────Refresh= 2secs ───16:48.29────────────────────────┐
------------------------------       For help type H or ...                                           │
│  #    #  #    #   ####   #    #        nmon -?  - hint                                              │
│  ##   #  ##  ##  #    #  ##   #        nmon -h  - full                                              │
│  # #  #  # ## #  #    #  # #  #                                                                     │
│  #  # #  #    #  #    #  #  # #       To start the same way every time                              │
│  #   ##  #    #  #    #  #   ##        set the NMON ksh variable                                    │
│  #    #  #    #   ####   #    #                                                                     │
│  ------------------------------                                                                     │
│                                                                                                     │
│  Use these keys to toggle statistics on/off:                                                        │
│     c = CPU        l = CPU Long-term   - = Faster screen updates                                    │
│     m = Memory     j = Filesystems     + = Slower screen updates                                    │
│     d = Disks      n = Network         V = Virtual Memory                                           │
│     r = Resource   N = NFS             v = Verbose hints                                            │
│     k = kernel     t = Top-processes   . = only busy disks/procs                                    │
│     h = more options                 q = Quit                                                       │
│─────────────────────────────────────────────────────────────────────────────────────────────────────│
```


### 1.2 显示CPU信息

```
│ CPU Utilisation ──────────────────────────────────────────────────────────────
│---------------------------+-------------------------------------------------+
│CPU  User%  Sys% Wait% Idle|0          |25         |50          |75       100|
│  1  15.2   1.5   0.0  83.2|UUUUUUU           >                              |
│  2   7.1   2.6   0.0  90.3|UUUs             >                               |
│  3  17.5   1.0   0.5  81.0|UUUUUUUU        >                                |
│  4  13.6   3.0   0.0  83.3|UUUUUUs            >                             |
│---------------------------+-------------------------------------------------+
│Avg  13.4   2.1   0.3  84.3|UUUUUUs        >                                 |
│---------------------------+-------------------------------------------------+

```

### 1.3 显示内存信息
```
│ Memory Stats ──────────────────────────────────────────────────────────────────
                RAM     High      Low     Swap    Page Size=4 KB
│ Total MB      7843.5     -0.0     -0.0   3916.0
│ Free  MB      1259.8     -0.0     -0.0   3915.6
│ Free Percent    16.1%   100.0%   100.0%   100.0%
│             MB                  MB                  MB
│                      Cached=  2912.7     Active=  3842.7
│ Buffers=   929.4 Swapcached=     0.0  Inactive =  1789.8
│ Dirty  =     0.1 Writeback =     0.0  Mapped   =   946.5
│ Slab   =   799.7 Commit_AS =  6624.9 PageTables=    40.2

```

### 1.4 显示磁盘信息

```
 Disk I/O ──/proc/diskstats────mostly in KB/s─────Warning:contains duplicates────
│DiskName Busy  Read WriteKB|0          |25         |50          |75       100|
│sda        5%    0.0   42.0|WW>                                              |
│sda1       0%    0.0    0.0|>                                                |
│sda2       0%    0.0    0.0|>                                                |
│sda5       5%    0.0   42.0|WW>                                              |
│sda6       0%    0.0    0.0|>                                                |
│Totals Read-MB/s=0.0      Writes-MB/s=0.1      Transfers/sec=12.0 

```

### 1.5 显示网络信息

```
 Network I/O ────────────────────────────────────────────────────────────────────
│I/F Name Recv=KB/s Trans=KB/s packin packout insize outsize Peak->Recv Trans 
│      lo     0.0     0.0       0.0      0.0     0.0    0.0        0.0     0.0 
│   wlan0     0.1     0.0       0.5      0.0   215.0    0.0        0.1     0.0 
│ docker0     0.0     0.0       0.0      0.0     0.0    0.0        0.0     0.0 
│    eth0     0.0     0.0       0.0      0.0     0.0    0.0        0.0     0.0
│ Network Error Counters ─────────────────────────────────────────────────────────
│I/F Name iErrors iDrop iOverrun iFrame oErrors   oDrop oOverrun oCarrier oColls
│      lo       0       0       0       0       0       0       0       0       0
│   wlan0       0       0       0       0       0       0       0       0       0
│ docker0       0       0       0       0       0       0       0       0       0
│    eth0       0       0       0       0       0       0       0       0       0
```

### 1.6 显示内核信息
```
Kernel Stats ────────────────────────────────────────────────────────────────────
│ RunQueue              3   Load Average    CPU use since boot time
│ ContextSwitch    2735.0    1 mins  0.54    Uptime Days=  0 Hours= 7 Mins=58
│ Forks               0.5    5 mins  0.86    Idle   Days=  1 Hours= 1 Mins=36
│ Interrupts        836.9   15 mins  0.92    Average CPU use=-221.51%

```

## 2. ethstatus
`ethstatus`可以实时监控网卡的带宽占用。

```
───────────────────.EthStatus v0.4a.──────────────────────────────[0/910]──────────────────────│
│                                                                                              │
│                                                                                              │
│                      ######                                                                  │
│                                                                                              │
│         ###                                                                                  │
│        ON/OFF                      RX  TX                                                    │
│                            0.00 B/s   0 Packets/s                                            │
│                                                                                              │
│                                                                                              │
│     IP Address:          192.168.1.116                                                       │
│     Interface name:      wlan0         Graphic Using Speed:    9.77 MB/s                     │
│     Top Speed:           732.00 B/s    Top Packets/s:          5                             │
│     Received Packets:    593428        Transmited Packets:     437965                        │
│     Received:            399.68 MB     Transmitted:            54.73 MB                      │
│     Errors on Receiving: 0             Errors on Transmission: 0                             │
│                                                                                              │
│                                                                                              │

```

## 3. iftop监控网卡实时流量
常用参数列表

| 参数 | 含义                                            | 例子                          |
| --   | --                                              | --                            |
| -i   | 指定需要监测的网卡                              | sudo iftop -i wlan0           |
| -n   | 将输出的主机信息都通过ip显示，不进行DNS方向解析 | sudo iftop -n -i wlan0        |
| -B   | 将输出以byte为单位显示网卡流量，默认是bit       | sudo iftop -B -i wlan0        |
| -p   | 以混杂模式运行iftop,此时iftop可以作为网络嗅探器 | sudo iftop -p -i wlan0        |
| -N   | 只显示链接端口号，不显示对应的服务名称          | sudo iftop -N -i wlan0        |
| -P   | 显示主机及端口信息                              | sudo iftop -P -i wlan0        |
| -F   | 显示特定网段的网卡进出流量                      | sudo iftop -F 192.168.1.10/24 |

```
                    12.5kb              25.0kb               37.5kb              50.0kb         [0/37]
+-------------------+-------------------+--------------------+-------------------+--------------------
192.168.1.116                         => 101.227.160.102                       17.6kb  3.55kb  1.90kb 
                                      <=                                       7.29kb  1.50kb   807b
224.0.0.251                           => 192.168.1.165                            0b      0b      0b  
                                      <=                                       1.72kb  2.21kb   566b
192.168.1.116                         => 140.206.160.161                       4.89kb  0.98kb  1.21kb
                                      <=                                       1.18kb   242b    302b
192.168.1.116                         => 192.168.1.1                              0b    112b     96b  
                                      <=                                          0b    378b    254b
239.255.255.250                       => 192.168.1.148                            0b      0b      0b
                                      <=                                       1.19kb   486b    730b
224.0.0.251                           => 192.168.1.166                            0b      0b      0b
                                      <=                                        272b     54b     14b
192.168.1.165                         => all-routers.mcast.net                    0b     26b      6b
                                      <=                                          0b      0b      0b
192.168.1.116                         => 101.226.76.164                           0b      0b   2.11kb
                                      <=                                          0b      0b    752b  
192.168.1.116                         => dev                                      0b      0b   1.50kb
                                      <=                                          0b      0b    544b
192.168.1.116                         => stage                                    0b      0b   1.40kb
                                      <=                                          0b      0b    504b
239.255.255.250                       => 192.168.1.135                            0b      0b      0b
                                      <=                                          0b      0b    161b
239.255.255.250                       => 192.168.1.113                            0b      0b      0b
                                      <=                                          0b      0b    160b
------------------------------------------------------------------------------------------------------
TX:             cum:   51.5kB   peak:   44.7kb                        rates:   22.4kb  4.64kb  8.27kb
RX:                    46.8kB           28.5kb                                 11.6kb  4.87kb  4.94kb
TOTAL:                 98.4kB           73.2kb                                 34.1kb  9.51kb  13.2kb
```

## 4. mtr
mtr是Linux中有一个非常棒的网络连通性判断工具,它结合了`ping`，`traceroute`，`nslookup`的相关特性。
相对于其他命令仅仅收集传输路径或响应时间，MTR 工具会收集更多的信息，比如 连接状态，连接可用性，以及传输路径中主机的响应性。

常用参数

| 参数       | 说明                                          |
| --         | --                                            |
| -s         | 用来指定ping数据包的大小                      |
| `--no-dns` | 不对IP地址做域名解析                          |
| -a         | 使用这个参数来设置ICMP返回之间的要求默认是1秒 |
| `--report` | 给主机发送10个ICMP包，然后输出报告            |


```
                    My traceroute  [v0.85]                                     [0/102]
localhost (0.0.0.0)                            Sun Mar 26 20:25:43 2017
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                            Packets               Pings
 Host                                    Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. 192.168.0.1                           0.0%     8    1.5   2.3   1.3   4.3   0.8
 2. 192.168.1.1                           0.0%     8    1.7   4.6   1.7  13.7   5.2
 3. 114.245.248.1                         0.0%     8   78.3 108.3   3.7 363.8 149.8
 4. 123.126.26.45                         0.0%     8    7.9  19.6   3.8  76.7  26.3
 5. 61.148.158.53                         0.0%     8    7.2  12.2   5.1  31.6   9.2
 6. 61.51.115.106                         0.0%     8   78.8  15.4   4.6  78.8  25.7
 7. 61.49.168.110                         0.0%     8    4.9   7.1   4.8  12.8   2.7
 8. ???
 9. ???
10. 61.135.169.121                        0.0%     7    4.3   5.9   4.2  10.9   2.2

```
**说明**
当分析mtr的输出时，需要注意两点:loss和latency
如果你在任何一跳上看到loss的百分比，这就说明这一跳上可能有问题。当然也有可能是服务提供商人为限制了ICMP发送的速率，也会导致丢包。如果下一跳没有丢包，那说明该丢包是人为限制的。
如果第4跳和第5跳都有50%的丢包率，从接下来的几跳都有丢包现象，所以不像是人为限制 ICMP 速率的原因。但是最后几跳都是30%的丢包率，我们可以猜测30%的丢包率除了网络糟糕的原因之前还有人为限制ICMP。所以，当我们看到不同的丢包率时，通常要以最后几跳为准。当出现短暂的10%丢包率的时候，不必担心，应用层的程序会弥补这点损失。

## 5. htop
常用功能键

| 按键 | 说明                                       |
| --   | --                                         |
| F1   | 帮助                                       |
| F2   | 设置                                       |
| F3   | 搜索进程                                   |
| F4   | 过滤器，按关键字搜索                       |
| F5   | 显示树形结构                               |
| F6   | 选择排序方式                               |
| F7   | 减少nice值，可以提高对应进程的优先级       |
| F8   | 增加nice值，可以降低对应进程的优先级       |
| F9   | 杀掉选中的进程                             |
| F10  | 退出                                       |
| /    | 搜索字符                                   |
| h    | 显示帮助                                   |
| l    | 显示进程打开的文件 lsof                    |
| u    | 显示所有用户，并可以选择某一特定用户的进程 |
| s    | 将调用strace追踪进程的系统调用             |
| t    | 显示树形结构                               |
| H    | 显示/隐藏用户线程                          |
| I    | 倒转排序                                   |
| K    | 显示/隐藏内核线程                          |
| M    | 按内存占用排序                             |
| P    | 按CPU排序                                  |
| T    | 按运行时间排序                             |

htop的左上角显示CPU、内存、交换区的使用情况，右边显示任务、负载、开机时间，下面是进程实时状态。
最后一行是F1～F10的功能菜单和对应的字符快捷键。

## 6. linux系统性能分析
分析次序为cpu mem disk network
