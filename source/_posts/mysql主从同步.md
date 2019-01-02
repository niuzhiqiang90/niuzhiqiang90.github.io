---
title: mysql主从同步
date: 2015-11-04 20:52:11
categories: mysql
tags: mysql
---

## 1. 安装mysql
[mysql下载地址](https://dev.mysql.com/downloads/mysql/)
```
$ tar -zxvf mysql-5.5.57-linux-glibc2.12-x86_64.tar.gz
$ mv mysql-5.5.57-linux-glibc2.12-x86_64 mysql-5.5.57
$ sudo ln -s `pwd`/mysql-5.5.57 /usr/local/mysqld
```

## 2. 配置mysql
配置文件为`/etc/my.cnf`
一般配置内容为
```
[mysqld]
user        = USERNAME
basedir     = /usr/local/mysqld
datadir     = /mnt/data/dbs/mysqld/data
bind-address=127.0.0.1
port        = 3306
socket      = /tmp/mysql.sock
skip-external-locking
key_buffer_size = 16M 
max_allowed_packet = 1M
table_open_cache = 64
sort_buffer_size = 512K
net_buffer_length = 8K
read_buffer_size = 256K
read_rnd_buffer_size = 512K
myisam_sort_buffer_size = 8M

[mysqld_safe] 
log-error   = /mnt/data/dbs/mysqld/logs/mysqld.log 
```
根据需求修改`绑定IP`、`端口`、`数据目录`、`日志目录`等内容即可，剩下的可以使用默认配置。

**配置主从同步，需要分别在`master`和`slaver`的`[mysqld]`区域中添加如下：**
```
log-bin=mysql-bin
server-id=1
```
**注意：两个配置文件中`server-id`的值不能相同**  
例如：可以将`slaver`的值设置为`2`
```
server-id=2
```

## 3. 创建目录
```
$ mkdir -p /mnt/data/dbs/mysqld/{data,logs}
```

## 4. 启动mysql

### 4.1 初始化
```
$ cd mysql-5.5.57/scripts
$ mysql_install_db --user=USERNAME
```

### 4.2 启动
在主从服务器上分别启动mysql  
[mysqld-svc脚本](https://github.com/niuzhiqiang90/toolkit/blob/master/bin/mysqld-svc)
```
$ mysqld-svc start
```

## 5. 授权
主数据库中进行授权  
授权用户`zhangsan`在`192.168.1.150`上的认证信息
注：`192.168.1.150`为`slaver`服务器的IP
```
> grant replication slave on *.* to 'zhangsan'@'192.168.1.150' identified by '123456';
```

## 6. 在从数据库中进行设置

### 6.1 在主数据库上查看相关信息
```
$ mysql -uroot -p
> show master status;
```

### 6.2 在从数据修改配置
```
$ mysql -uroot -p
> change master to
master_host='主服务器IP'，
master_user='zhangsan'
master_password='123456',
master_log_file='mysql-bin.00000序号'，
master_log_pos=数字；
```
**上述的信息会存在master.info中**

## 7. 启动slave
在从服务器登陆`mysql`，并启动`slave`
```
$ mysql -uroot -p
> start slave;
> show slave status \G;
```
如果结果中
```
Slave_IO_Running : Yes
Slave_SQL_Running : Yes
```
说明基本配置成功！

## 8. 测试主从同步
1. 在主库中写个数据，看是否同步到备库。
2. 查看`master.info`