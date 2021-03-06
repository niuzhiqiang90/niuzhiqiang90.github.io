---
title: Tcp wrappers
date: 2015-09-13 16:58:27
tags: linux
---


## 1. 作用
针对服务或程序的访问控制

## 2. 配置文件
/etc/hosts.allow（**设置允许访问的服务**）  
/etc/hosts.deny	（**设置拒绝访问的服务**）

## 3. 策略格式
**服务名称:客户机地址列表**   也可用**ALL:ALL**  
如果设置某一个服务，那就直接写服务名称；
如果想设置允许所有服务，就写一个ALL。如果允许所有地址，就在地址写ALL  
<font color=red>ALL:ALL 允许所有服务被所有地址访问（在hosts.allow文件中）  
拒绝所有服务被所有地址访问（在hosts.deny文件中） </font>  
如果不想设置单个的IP，也不想设置所有的IP，**可以用通配符?和*来设置**，也可以来声明一个网段或者声明一个域名都是可以的。

**网段地址**  
如192.168.4<font color=red>.</font> 或者192.168.1.0/255.255.255.0<font color=red>（不支持/8、/16、/24格式）</font>

**区域地址**   
如.abc.com

例如：  
在`hosts.allow`中添加如下内容

```
sshd:192.168.2??.252 	（表示只要是2开头，后面有两位的网段都允许访问）
sshd:192.168.* 表示前边是192.168后面任意的网段 
192.168.1. 代表 192.168.1.0/255.255.255.0（192.168.1.0网段）
sshd:www. 代表sshd服务，不能被www开头的主机访问
```

|    内容    |    说明    |  
| ---|---|  
|    sshd:192.168.2??.252    | 	（表示只要是2开头，后面有两位的网段都允许访问）    |
|    sshd:192.168.*    |    表示前边是192.168后面任意的网段    |
|    192.168.1.    |    代表 192.168.1.0/255.255.255.0（192.168.1.0网段）    |
|    sshd:www.     |    代表sshd服务，不能被www开头的主机访问    |
**<font color=red>注意：以.开头或者以.结束的格式里不能使用通配符</font>**

## 4. 规则
1. 不做任何配置时，默认允许访问
2. 定义网段时，不能使用/24的方式
3. 可以使用**<font color=red>.</font>**作为开始或结
4. 以**<font color=red>.</font>**开头或者以**<font color=red>.</font>**结束的格式里**<font color=red>不能使用通配符</font>**
5. 应用顺序，先allow，后den
6. 允许个别，拒绝所有时，在allow文件中添加**服务名称:允许的主机IP**后，在deny文件中添加**服务名称:ALL**
7. 允许所有，拒绝个别时，allow中为空即可

**例**  
在主机A上编辑`/etc/hosts.deny`添加  

```
ssh:主机B的地址
```
**不需要重启任何服务，它是即时生效的**
此时，之前可以ssh连接到主机A的主机B，已经不能再通过ssh服务连接到主机A了  
把同样的内容写入`/etc/hosts.allow`就是代表允许主机B通过ssh访问主机A  
**一般设置允许的话，不需要这样做，因为默认就是允许的**  
**<font color=red>先看allow后看deny，如果同一条策略两个文件里都写了，那么allow里的文件生效</font>**

