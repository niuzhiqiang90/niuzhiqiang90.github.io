---
title: Samba案例
date: 2015-09-18 10:46:04
tags: Samba
---
## 1. 案例说明
创建3个文档目录
`/var/share/public`，存放公共数据
`/var/share/training`，存放技术培训资料
`/var/share/devel`，存放项目开发资料
将`/var/share/public`目录共享为`public`所有员工可匿名访问，但是只能读取文件，不能写入
将`/var/share/training`目录共享为`peixun` 只允许管理员admin及技术部的员工只读访问
将`/var/share/devel/`目录共享为`kaifa`技术部的员工都可以读取该目录中的文件，但是只有管理员`admin`及`abc`项目组的员工有写入权限
  
## 2. 配置
```
[global]
    workgroup = WORKGROUP
    server string = SambaServer Version %
    log file = /var/log/samba/log.%m
    security = user
    max log size = 50  
               
    passdb backend =tdbsam
    load printers = yes
    cups options = raw
    #obtain list ofprinters automatically on SystemV

[homes]
    comment = HomeDirectories
    browseable = no
    writable = yes
    
[printers]
    comment = All Printers
    path =/var/spool/samba
    browseable = no
    guest ok = no
    writable = no
    printable = yes

[public]
    comment = public
    path = /var/share/public
    public = yes

[peixun]
    comment = training
    path = /var/share/training
    valid users = admin,@jishubu
    read only = yes

[kaifa]
    comment = devel
    path = /var/share/devel
    valid users = admin,@abc,@jishubu
    write list = admin,@abc
```