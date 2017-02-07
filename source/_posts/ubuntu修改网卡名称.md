---
title: ubuntu修改网卡名称
date: 2017-02-07 11:21:00
tags: ubuntu
---

今天强迫症又犯了，想要把网卡的名称改为eth0和wlan0
按照网上的方法试了几种，都没有成功，下面记录下我修改成功的方法。

## 1. 修改配置文件
Open the file /etc/udev/rules.d/70-persistent-net.rules with your favorite editor.
Find the rule matching your MAC address.
Change the NAME value from eth1 to eth0 i.e. NAME="eth0"

```
$ sudo vi /etc/udev/rules.d/70-persistent-net.rules
```
找到你要修改的网卡的MAC，修改对应的`NAME`值
```
# PCI device 0x10ec:0x8168 (r8169)
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="50:7b:9d:55:47:2e", ATTR{dev_id}=="0x0", ATTR{type}=="1", KERNEL=="eth*", NAME="eth0"
 
# PCI device 0x168c:0x0042 (ath10k_pci)
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="30:52:cb:68:81:17", ATTR{dev_id}=="0x0", ATTR{type}=="1", KERNEL=="wlan*", NAME="wlan0"
```

## 2. 使修改生效
```
$ sudo udevadm trigger
```
运行`ifconfig`查看网卡名称是否已经修改过来，如果没成功，就重启一下吧。

[From askubuntu](http://askubuntu.com/questions/637591/my-ethernet-interface-name-has-changed-from-eth0-to-eth1)



