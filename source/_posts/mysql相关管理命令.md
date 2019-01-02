---
title: mysql相关管理命令
date: 2015-11-12 15:12:02
categories: mysql
tags: mysql
---

## 1. mysql启动和关闭

### 1.1 启动方法

方法一：
```
[root@localhost ~]# /usr/local/mysql/bin/mysqld_safe --user=mysql &
```

方法二：
```
[root@localhost ~]# /etc/init.d/mysqld start
```

方法三：
前提是`mysql.server`的启动脚本拷贝到了`/etc/init.d`目录下
```
[root@localhost ~]# service mysqld start  
```

### 1.2 关闭方法

方法一：
```
[root@localhost ~]# mysqladmin -uroot -p123456 shutdown
```

方法二：
```
[root@localhost ~]# /etc/init.d/mysqld stop
```

## 2. 登陆`mysql`的方法
```
mysql  -uroot  -p
```
如果没有设置密码，直接执行
```
mysql
```
也可以。


## 3. 设置及修改mysql root用户密码
### 3.1 第一次设置密码
单实例：
```
[root@localhost ~]# mysqladmin -u root password '123456'
```

多实例：
```
[root@localhost ~]# mysqladmin -u root password '123456' -S /data/3306/mysql.sock
```

### 3.2 修改密码

未登陆进数据库  
单实例：
```
[root@localhost ~]# mysqladmin -u root -p123456 password '654321'
```

多实例：
```
[root@localhost ~]# mysqladmin -u root -P123456 password '654321' -S /data/3306/mysql.sock
```

已登陆进数据库
```
# 注意，一定要用password()函数来设置密码，这样设置的密码是加密的
mysql> update mysql.user set password=password(654321) where user='root' and host='localhost';
# 更新权限
mysql> flush privileges;
```

## 4. mysql密码找回

### 4.1 准备工作

因为在重新设置`mysql`的`root`密码的间隙，数据库完全处于没有密码保护的状态下，其他的用户也可以任意地登录和修改`mysql`的信息，所以<font color="red">首先要确认服务器处于没有人能够任意地连接`mysql`数据库的安全状态。</font>可以将`mysql
`的对外端口临时关闭，并且停止所有可能连接`mysql`的进程，例如：`nginx`。最安全的状态是到服务器的Console上面操作，并且拔掉网线。

### 4.2 修改mysql的登录设置：

在配置文件`/etc/my.cnf`的`[mysqld]`区域中加上一句：`skip-grant-tables`  
例如：
```
[mysqld]
datadir=/mnt/data/dbs/mysqld/data
socket=/var/lib/mysql/mysql.sock
skip-grant-tables
```

### 4.3 重启mysqld
```
$ /etc/init.d/mysqld restart
```

### 4.4 登录并修改root密码
```
$ /usr/bin/mysql
mysql> use mysql;
mysql> update user set password = password ('654321') where user = 'root';
mysql> flush privileges ;
mysql> quit
```

### 4.5 将登录设置修改回来
将刚才在`[mysqld]`区域中加上的`skip-grant-tables`删除。

### 4.6 重启mysqld
```
$ /etc/init.d/mysqld restart
```
