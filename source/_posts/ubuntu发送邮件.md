---
title: ubuntu发送邮件
date: 2017-05-16 21:23:23
tags:
---

## 1. 安装
```
sudo apt-get install msmtp
sudo apt-get install mutt
```

## 2. 配置

### 2.1 配置mutt
```
vi ~/.muttrc 
```
或 
```
sudo vi /etc/Muttrc
```
添加如下内容：
```
set sendmail="/usr/bin/msmtp"
set use_from=yes
set realname="username"
set from=username@xxx.cn
set envelope_from=yes

set folder="/var/mail"    # Contains all the mailboxes
set record="+username"
```
确认文件权限
```
ll /etc/Muttrc
-rw-r--r-- 1 root root 125 May 16 11:13 /etc/Muttrc
```



### 2.2 配置msmtp
```
vi ~/.msmtprc 
```
或 
```
sudo vi /etc/msmtprc
```
添加如下内容
```
account default
host    smtp.ym.163.com  # 邮件服务器地址
user    username@xxx.cn
from    username@xxx.cn 
password    ***********
auth    plain
tls     off
logfile /var/log/username/msmtp.log  # 日志位置 或 logfile ~/.msmtp.log
```


### 2.3 设置msmtp配置文件权限

由于密码是明文，所以修改文件的权限，只允许自己可以访问该文件
```
chmod 600 ~/.msmtprc
```
或者
```
sudo chown username:username /etc/msmtprc
chmod 600 /etc/msmtprc
```
确认文件权限
```
ll /etc/msmtprc 
-rw------- 1 username username 190 May 16 11:09 /etc/msmtprc
```

### 2.4 创建日志文件
```
touch ~/.msmtp.log
```
## 3. 验证
```
msmtp --host=smtp.ym.163.com --serverinfo

SMTP server at smtp.ym.163.com ([223.252.213.139]), port 25:
    mr213139.mail.yeah.net ESMTP HMail (1.0)
Capabilities:
    SIZE 71680000:
        Maximum message size is 71680000 bytes = 68.36 MiB
    PIPELINING:
        Support for command grouping for faster transmission
    ETRN:
        Support for RMQS (Remote Message Queue Starting)
    STARTTLS:
        Support for TLS encryption via the STARTTLS command
    AUTH:
        Supported authentication methods:
        PLAIN LOGIN 
This server might advertise more or other capabilities when TLS is active.
```


## 4. 使用
```
echo "hello world" | mutt -s "title" 目标邮箱
```

```
mutt -s "测试" username@xxx.cn < gotty 
```



