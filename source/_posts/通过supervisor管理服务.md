---
title: 通过supervisor管理服务
date: 2018-05-09 20:54:10
categories: linux
tags: supervisor
---

## 1. 简介
[supervisor](http://supervisord.org)是一个用Python写的进程管理工具，可以很方便的用来启动、重启、关闭进程。不仅可以控制单个进程，还可以同时启动、关闭多个进程。

## 2. 安装
```
pip install --upgrade pip
pip install supervisor
```

## 3. 配置
配置文件为`/etc/supervisord.conf`

### 3.1 生成配置文件
```
sudo echo_supervisord_conf > /etc/supervisor/supervisord.conf
```
### 3.2 配置项
```
; supervisor config file                                                                                                                                                                                        

[unix_http_server]
file=/var/run/supervisor.sock   ; (the path to the socket file)
chmod=0700                       ; sockef file mode (default 0700)
 
[supervisord]
logfile=/var/log/supervisor/supervisord.log ; (main log file;default $CWD/supervisord.log)
pidfile=/var/run/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
childlogdir=/var/log/supervisor            ; ('AUTO' child log dir, default $TEMP)

minfds=2000000
 
; the below section must remain in the config file for RPC 
; (supervisorctl/web interface) to work, additional interfaces may be
; added by defining them in separate rpcinterface: sections
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface
 
[supervisorctl]
serverurl=unix:///var/run/supervisor.sock ; use a unix:// URL  for a unix socket
 
; The [include] section can just contain the "files" setting.  This
; setting can list multiple files (separated by whitespace or
; newlines).  It can also contain wildcards.  The filenames are 
; interpreted as relative to this file.  Included files *cannot*
; include files themselves.
 
[include]
files = /etc/supervisor/conf.d/*.conf
```
### 3.3 创建目录
```
sudo mkdir /var/log/supervisor
sudo mkdir /etc/supervisor/conf.d
```

## 4. 启动
```
sudo supervirsord
```
如果有报错，修改刚才创建的目录权限
```
sudo chmod 777 /var/run/
sudo chmod 777 /var/log/supervisor
```

## 5. 添加管理项
待服务启动后，就可以添加需要`supervisor`管理的服务

### 5.1 创建服务配置文件
将需要被管理的配置文件放到`/etc/supervisor/conf.d/`下  
**需要用到的环境变量通过`environment`来设置即可，多个环境变量用`,`分割**  
例如：创建`consul.conf`文件内容如下
```
[program:consul]                                                                                                                                                                                                
user=USERNAME
environment=GOMAXPROCS=4
command=/home/USERNAME/opt/consul/bin/consul agent -domain "test." -config-file /home/USERNAME/opt/consul/conf/consul.json
autostart=true
autorestart=true
stdout_logfile=/mnt/data/logs/supervisor/consul-out.log
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
stdout_capture_maxbytes=1MB
stderr_logfile=/mnt/data/logs/supervisor/consul-err.log
stderr_logfile_maxbytes=1MB
stderr_logfile_backups=10
stderr_capture_maxbytes=1MB
```

### 5.2 reload配置文件

``` 
sudo supervisorctl reread
```

### 5.3 更新配置文件
更新配置文件后，服务会自动添加到`supervisor`列表里，并自动把服务器管理起来。
```
sudo supervisorctl update consul
```

### 5.4 测试
`consul`启动后，人为`kill`掉`consul`，测试`supervisord`是否会自动将`consul`重启。

## 6. 其他相关命令

### 6.1 重启supervisord
```
sudo supervisorctl reload
```

### 6.2 更新配置文件
`PROGRAM-NAME`为可选参数，不指定为所有。
```
sudo supervisorctl reread
sudo supervisorctl update [PROGRAM-NAME]
```

### 6.3 管理服务
`PROGRAM-NAME`为可选参数，不指定为所有。
```
sudo supervisorctl start [PROGRAM-NAME]
sudo supervisorctl stop [PROGRAM-NAME]
sudo supervisorctl restart [PROGRAM-NAME]
```

### 6.4 查询所有服务状态
```
sudo supervisorctl status
```
