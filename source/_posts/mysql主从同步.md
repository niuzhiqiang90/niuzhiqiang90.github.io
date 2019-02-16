---
title: mysql主从同步
date: 2015-11-04 20:52:11
categories: mysql
tags: mysql
---

## 1. 安装mysql
### 1.1 安装依赖
`mysql`依赖`libaio`，如果不安装，`data`目录初始化和`mysql`启动都会失败。
```
$ sudo apt-get install libaio1
```
### 1.2 安装`mysql`
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
**注意：两个配置文件中`server-id`的值不能相同！**  
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

### 4.3 验证
验证`log-bin`是否开启
```
$ mysql -uroot -p123456 -e "show variables like 'log_bin';"
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_bin       | ON    |
+---------------+-------+
1 row in set (0.01 sec)
```

## 5. 授权
主数据库中进行授权  
授权用户`zhangsan`在`192.168.1.150`上的认证信息
注：`192.168.1.150`为`slaver`服务器的IP
```
mysql> grant replication slave on *.* to 'zhangsan'@'192.168.1.150' identified by '123456';
```

## 6. 在从数据库中进行设置

### 6.1 在主数据库上查看相关信息
```
$ mysql -uroot -p
mysql> flush table with read lock; # 锁表，只允许读操作，不允许写操作
mysql> show master status;  # 查看bin-log目前最新的状态
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000011 |      107 |              |                  |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)
```
这时不要退出数据库，另开一个ssh会话进行下面的操作备份主库
```
$ mysqldump -uroot -p123456 -A -B --events > ~/rep.sql
```
`-A`意思是备份所有库；  
`--events`是备份事件；

解锁，允许写入
```
mysql> unlock tables;
```

将主数据库服务器的备份文件传输到从数据库服务器
```
scp rep.sql USERNAME@192.168.1.150:~/
```
 
将主数据库服务器的备份导入
```
$ mysql -uroot -p123456 < ~/rep.sql
```
**实际生产环境中，在做好主库备份并解锁后，还会有新的数据写入，之后的增量可以通过主从同步的方式进行同步了。**

### 6.2 在从数据修改配置
```
$ mysql -uroot -p
mysql> change master to
master_host='主服务器IP'，
master_user='zhangsan'   #从库请求同步时的身份
master_password='123456', 
master_log_file='mysql-bin.00000序号'，  #从哪个节点开始增量
master_log_pos=数字；
```
**上述的信息会存在master.info中**

## 7. 启动slave
在从数据库服务器登陆`mysql`，并开启主从同步功能
```
$ mysql -uroot -p
mysql> start slave;
mysql> show slave status \G;
```
如果如下两个线程结果为`Yes`
```
Slave_IO_Running : Yes
Slave_SQL_Running : Yes
```
说明基本配置成功！

## 8. 测试主从同步
1. 在主库中写个数据，看是否同步到备库。
2. 查看`master.info`

## 9. 生产环境快速配置`mysql`主从同步
1. 安装配置好从库的数据库，配置好`server-id`参数；
2. 无需配置主库`my.cnf`文件，主库的`log-bin`和`server-id`参数默认就是配置好的；
3. 登录主库增加用于从库连接主库同步的账号，例如：`zhangsan`，并授权`replication slave`同步的权限；
4. 使用之前`mysqldump`带`--master-data=1`备份的全备数据恢复到从库；
5. 在从库执行`change master to ..`语句，无需`binlog`文件及对应位置点的参数；
6. 从库开启同步开关，`start slave`；
7. 从库`show slave status\G`，检查同步状态，并在主库进行更新测试。
