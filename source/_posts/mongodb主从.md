---
title: mongodb主从
date: 2018-12-11 20:44:12
tags: mongodb
---

## 1. 安装
根据需求从[这里](https://www.mongodb.com/download-center/community)下载相应的版本即可。  
例如下载完后放到`~/opt`
```
cd ~/opt
tar -zxvf mongodb-linux-x86_64-ubuntu1604-4.1.6.tgz
mv mongodb-linux-x86_64-ubuntu1604-4.1.6 mongodb-4.1.6
```

## 2. 配置

### 2.1 创建配置目录
在`master`和`slaver`上创建配置文件目录
```
cd ~/opt/mongodb-4.1.6
mkdir conf
```

### 2.2 配置项
配置文件可以放到`~/opt/mongodb-4.1.6/conf`里

master配置文件`mongod.conf`内容如下
```
dbpath=/mnt/data/dbs/mongodb/data
logpath=/mnt/data/dbs/mongodb/logs/mongod.log

logappend=true
port = 27017

# Listen to local interface only. Comment out to listen on all interfaces. 
bind_ip = 10.211.55.6,127.0.0.1

directoryperdb = true
profile = 1 

master = true
```

slaver的配置文件`mongod.conf`内容如下
```
dbpath=/mnt/data/dbs/mongodb/data
logpath=/mnt/data/dbs/mongodb/logs/mongod.log

logappend=true
port = 27017

# Listen to local interface only. Comment out to listen on all interfaces. 
bind_ip = 10.221.55.7,127.0.0.1
 
directoryperdb = true
profile = 1 
 
source = 10.211.55.6:27017
slave = true
```

### 2.3 创建数据目录
在`master`和`slaver`上创建数据目录

```
mkdir -p /mnt/data/dbs/mongodb/{data,logs}
```

### 2.4 启动并测试
在`master`和`slaver`上分别启动`mongodb`

```
cd ~/opt/mongodb-4.1.6/
./bin/mongod --config ./config/mongod.conf
```
服务启动后，在`master`执行写入数据命令
```
./bin/mongo 
> #写入数据命令
```

在`slaver`上进行查询验证写入的数据。

## 3. 添加用户名密码
上述过程，`mongodb`主从就已经配置完成，但是这样是不安全的，生产环境通常需要对数据库设置用户名密码。

在`master`和`slaver`的配置文件中，分别添加
```
auth = true
```

### 3.1 添加root用户
```
./bin/mongo 
> use admin
> db.createUser(
{ 
user: "root",
pwd: "pwd4root",
roles: [ { role: "root", db: "admin" } ]
}
)
>
```

### 3.2 为数据库添加用户
```
./bin/mongo 
> use db_name
> db.createUser(
{
user: "dbowner",
pwd: "pwd4dbowner",
roles: [ { role: "dbOwner", db:"db_name"}]
}
)
```


### 3.3 删除错建用户
获取当前数据库的用户
```
./bin/mongo 
> use db_name
> db.auth("dbowner","pwd4dbowner")
> db.getUsers()
```

删除不需要的数据库用户
```
./bin/mongo 
> use db_name
> db.auth("dbowner","pwd4dbowner")
> db.dropUser("drop_username")
```

### 3.3 创建认证keyfile
在`master`服务器上生成`keyfile`  
修改权限为700  
复制到`slaver`服务器
```
cd /mnt/data/dbs/mongodb/
openssl rand -base64 741 >> keyfile 
chmod 700 keyfile
```

在`master`和`slaver`的配置文件中，分别添加
```
keyFile = /mnt/data/dbs/mongodb/keyfile
```
依次重启`master`和`slaver`服务器的`mongodb`

## 4. 测试
在`master`服务器进行数据写入操作  
在`slaver`服务器进行验证，新写入的数据同步了，包括数据库的用户认证信息也会同步。