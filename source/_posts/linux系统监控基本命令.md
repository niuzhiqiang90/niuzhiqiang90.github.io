---
title: linux系统监控基本命令
date: 2017-03-25 17:09:57
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
| --   |
| -i   | 指定需要监测的网卡                              | sudo iftop -i wlan0           |
| -n   | 将输出的主机信息都通过ip显示，不进行DNS方向解析 | sudo iftop -n -i wlan0        |
| -B   | 将输出以byte为单位显示网卡流量，默认是bit       | sudo iftop -B -i wlan0        |
| -p   | 以混杂模式运行iftop,此时iftop可以作为网络嗅探器 | sudo iftop -p -i wlan0        |
| -N   | 只显示链接端口号，不显示对应的服务名称          | sudo iftop -N -i wlan0        |
| -P   | 显示主机及端口信息                              | sudo iftop -P -i wlan0        |
| -F   | 显示特定网段的网卡进出流量                      | sudo iftop -F 192.168.1.10/24 |

```

```

## 4. mtr
mtr是Linux中有一个非常棒的网络连通性判断工具,它结合了`ping`，`traceroute`，`nslookup`的相关特性。
常用参数

| 参数     | 说明                                          |
| --       |
| -s       | 用来指定ping数据包的大小                      |
| -nno-dns | 不对IP地址做域名解析                          |
| -a       | 使用这个参数来设置ICMP返回之间的要求默认是1秒 |

```
                    My traceroute  [v0.85]                                     [0/102]
localhost (0.0.0.0)                            Sun Mar 26 20:25:43 2017
Keys:  Help   Display mode   Restart statistics   Order of fields   quit                                                                                                                          Packets               Pings
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
todo

## 5. htop
todo


## 6. linux系统性能分析
分析次序为cpu mem disk network
