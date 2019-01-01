---
title: mysql配置项修改
date: 2017-08-17 21:12:03
categories: mysql
tags: mysql
---

大多数情况下我们会想指定`mysql`的`basedir`和`datadir`或者`mysql`的启动用户。

## 1. 修改配置文件
`/etc/my.cnf`
添加如下内容
```
[mysqld]
basedir     = /home/USERNAME/opt/mysql-5.5.57 #mysql安装目录
datadir     = /mnt/data/mysqld/data #mysql数据目录
```
同时也可以修改mysql的启动用户
[Run MySQL as a Normal User](https://dev.mysql.com/doc/refman/5.5/en/changing-mysql-user.html)
```
user         = USERNAME   #修改mysql的用户
bind-address = 127.0.0.1 #修改绑定的ip
```

## 2. 同步数据文件
```
cd /mnt/data/mysqld/data
sudo rsync -avz /home/USERNAME/opt/mysql-5.5.57/data/* ./
sudo chown -R USERNAME:USERNAME ../data  #如果没有修改mysql的用户，该步忽略
```

## 3. 启动并检测
执行`mysql`相关命令来启动进程
```
mysqld-svc start
ps -ef | grep mysqld
```

## 4. 补充
修改mysql的日志文件位置
```
[mysqld_safe]
log-error   = /mnt/data/mysqld/logs/mysqld.log
```
记得创建日志文件目录。




