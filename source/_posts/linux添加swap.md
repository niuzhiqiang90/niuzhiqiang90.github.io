---
title: linux添加swap
date: 2017-02-14 14:39:33
tags: linux
---

## 1. swap说明
物理内存就是计算机的实际内存大小，由RAM芯片组成的。
虚拟内存则是虚拟出来的，使用磁盘代替内存。
虚拟内存的出现，让机器内存不够的情况得到部分解决。当程序运行起来由操作系统做具体虚拟内存到物理内存的替换和载。这里的虚拟内存就是swap

当物理内存使用完或者达到一定比例之后，我们可以使用swap做临时的内存使用。当物理内存和swap都被使用完那么就会出错，out of memory。

## 2. 使用dd创建swap

### 2.1 查看当前内存
```
×××@localhost: ~$ free -h
             total       used       free     shared    buffers     cached
Mem:          7.7G       6.0G       1.6G       875M       476M       3.2G
-/+ buffers/cache:       2.4G       5.3G
Swap:         0B         0B       0B
```

### 2.2 创建swap文件
```
×××@localhost: ~$ sudo -s
root@localhost:~# dd if=/dev/zero of=/mnt/swap_mount_point bs=1M count=2048
2048+0 records in
2048+0 records out
2147483648 bytes (2.1 GB) copied, 16.7229 s, 128 MB/s

root@localhost:~# ls -lh /mnt/swap_mount_point 
-rw-r--r-- 1 root root 2.0G Feb 14 18:08 /mnt/swap_mount_point

root@localhost:~# file /mnt/swap_mount_point 
/mnt/swap_mount_point: data

```

### 2.3 格式化swap文件
```
root@localhost:~# mkswap /mnt/swap_mount_point 
Setting up swapspace version 1, size = 2097148 KiB
no label, UUID=2ed6629c-4b31-499b-b21d-2235971f8678
root@localhost:~# file /mnt/swap_mount_point 
/mnt/swap_mount_point: Linux/i386 swap file (new style), version 1 (4K pages), size 524287 pages, no label, UUID=2ed6629c-4b31-499b-b21d-2235971f8678
```

### 2.4 挂载
```
root@localhost:~# swapon /mnt/swap_mount_point 
root@localhost:~# free -h
             total       used       free     shared    buffers     cached
Mem:          7.7G       7.5G       197M       878M       476M       4.7G
-/+ buffers/cache:       2.3G       5.3G
Swap:         2G       264K       2G
```

### 2.5 添加开机启动
```
root@localhost:~# echo "/mnt/swap_mount_point swap swap defaults 0 0" >> /etc/fstab  
```
