---
title: Linux强制重新获取IP
date: 2016-10-26 12:44:44
tags: linux
---

## 1. 释放现有IP

```
$ sudo dhclient -r //release ip
```

## 2. 获得IP
```
$ sudo dhclient
```
不需要重启网络服务，上面的命令适用于任何linux发行版，例如RHEL，Fedora，CentOS，Ubuntu或者其他的。
当然你也可以执行如下命令  

```
# ifdown eth0
# ifup eth0
```
OR

```
# /etc/init.d/networking restart
```