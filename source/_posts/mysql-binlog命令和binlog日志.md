---
title: mysql binlog命令和binlog日志
date: 2015-11-12 14:45:25
categories: mysql
tags: mysql
---
## 1. mysql的`binlog`日志作用
用来记录`mysql`增删改查等对`mysql`数据库有更新操作的命令记录。  
不对数据库内容产生修改的操作，像`select`、`show`命令都不会被记录。

## 2. `mysqlbinlog`命令

### 2.1 作用      
`bin-log`是二进制文件，`mysqlbinlog`用来解析这个二进制文件，也用来做增量备份。  
例如：
```
$ mysqlbinlog /mnt/data/dbs/mysqld/data/mysql-bin.000006 | less
```
 
 
### 2.2 分库
`binlog`文件记录的是所有库的增删改查记录。如果想要获取对某个库的操作,可以用`-d`选项截取指定库的`binlog`
```
$ mysqlbinlog -d testdb /mnt/data/dbs/mysqld/data/mysql-bin.000006 > testdb.sql
```
 
### 2.3 基于位置点的增量备份
例如：输出初始位置1990，结束位置2020的所有`binlog`日志导到`pos.sql`
```
$ mysqlbinlog /mnt/data/dbs/mysqld/data/mysql-bin.000006 --start-position=1990 --stop-position=2020 -r pos.sql
```
恢复
```
$ mysql -uroot -p123456 testdb < pos.sql
```
 
 