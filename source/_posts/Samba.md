---
title: Samba
date: 2015-09-17 09:47:26
tags: Samba
---


## 1. samba基础
`SMB`协议
`Server Message Block` 服务消息块
`CIFS`协议
`Common Internet File System` 通用互联网文件系统

[Samba项目](http://www.samba.org)
**作用：**
分享档案与打印机服务<font color=red>（不推荐用作打印机服务）</font>
可以提供用户登入SAMBA主机时的身份认证
可以进行Windows网络上的主机名解析（NetBIOS name:WINS服务）

## 2. 详细说明

### 2.1 软件包
`samba`、`samba-client`

### 2.2 主进程
`smbd`: 提供对服务器中文件、打印资源的共享访问 端口`139` `445`  
`nmbd`: 通过基于`NetBIOS`主机名称的解析 端口`137` `138`

### 2.3 samba的服务脚本 
`/etc/init.d/smb`

### 2.4 配置文件
| 配置文件 | 说明 |
| --- | -- |
| `/etc/samba/smb.conf`     |  主配置文件 |
|    `/etc/smbusers`   |    别名文件 </br>  samba是跨平台的，可能windows下的管理员或用户不习惯用root去登录samba，他可能更习惯administrator或admin这样的用户来登录。
|    `/etc/lmhosts`    |    用来提供NetBIOS的域名解析 </br>（现在很少使用，可以通过samba来搭一个WINS服务器，然后通过它来提供NetBIOS的域名解析）  
**<font color=red> `testparm`检查`samba`配置文件中是否有语法错误</font> **

### 2.5 服务名
`Samba`的服务名为`smb`

### 2.6 `smb.conf`文件的配置内容
注释行：以<font color=red>#</font>开头的行  
配置实例行：以<font color=red>;</font>号开头的行

```
$ cp /etc/samba/smb.conf  /etc/samba/smb.conf.bak
```

结合grep命令提取有效配置行
```
$ grep -v “^#” smb.conf | grep -v “^;” | grep -v “^$”  > smb.conf
```

#### 2.6.1 全局配置区域
配置`Samba`服务的运行方式，指定如何运行`samba`。
<font color=red>[global]</font> 

|    配置项    |    说明    |
|    ---    |
|    workgroup = MYGROUP    |    定义默认使用的工作组的名称</br>samba服务是能够跨平台使用的，如果想要跨平台使用，让windows平台能够通过工作组看到samba上共享出来的目录的话，名称一定要是windows工作组的名称    |
|    server string = %h server (Samba, Ubuntu) %v    | <font color=red>当前samba服务在登录时的提示信息 </br> %v可以看到当前登录的版本信息，应该去掉</font>    |
|    log file = /var/log/samba/log.%m    |  日志文件位置</br><font color=red>%m 为每个用户提供日志文件 log.zhangsan </font>    |
|    max log size = 500    |    最大的日志文件大小，超过大小进行日志轮替    |
|    security = share    |    身份验证的方式（安全级别）    </br>    share  表示samba服务允许匿名访问，所有人都能访问    </br>    user 表示允许本地服务器提供的samba用户来让客户端访问    </br>    server 表示允许通过其他主机进行身份认证    </br>    domain 表示允许通过域控制器进行身份认证    </br> 	 域：把计算机逻辑上集中到一起，集中管理    |
|    passdb backend = tdbsam    |    密码的加密方式    |
|    load printers = yes    |    加载打印机(如果本机连接有打印机，是否自动的去加载它) |
|    cups options = raw    |    对打印机的操作是读写操作    |

剩下的区域用来配置文件共享的
#### 2.6.2 用户的家目录区域
<font color=red>[homes]</font>

|    配置项    |    说明    |
|    ---    |
|    comment = Home Directories    |    家目录的信息    |
|    browseable = yes 或 no	    |  目录是否被网上邻居可见 </br>如果=no，共享出来的文件夹是看不到的，但是这个共享文件是存在的，在访问是要使用`\\ip地址\文件夹名`才能访问，提高安全性|
|    writable = yes    |    对共享目录的权限（可写）    |


#### 2.6.3 打印机配置区域
<font color=red>[printers]</font>

|    配置项    |    说明    |
|    ---    |
|    comment = All Printers     |    描述信息    |
|    path = /var/spool/samba    |    打印机的配置存放位置    |
|    browseable = yes 或 no    |    是否网上邻居可见    |
|    printable = yes     |    | 
|    guest ok = yes    |    允许所有人访问共享目录 （权限默认只读） </br> 等价于 public = yes     |
|    read only = yes    |
|    writable = yes    |     对共享目录的权限（可写）    |
|    printable = yes    |    是否启动打印机    |

#### 2.6.4  用户自定义配置区域
<font color=red>[share]</font>

|    配置项    |    说明    |
|    ----    |
|    comment = share    |    描述信息    |
|    path = /public    |    共享目录路径    </br> **<font color=red>真实目录的名称不一定要和定义的名称一样，[share]叫做共享名，/public是真实的共享目录，用户应该使用共享名进行访问  提高安全性 </font>**
|    guest ok = yes    |    有访问权限才能有写入之类的权限    |
|    browseable = no   |    网上邻居不可见    |
|    writable = yes    |    允许所有用户以写入的方式进行访问 <br> **<font color=red>除了受到服务的影响还受到目录权限的限制（取两个中比较严格的权限） 把一个权限设置到最大，通过另一个来限制权限</font>**

#### 2.6.5 对访问权限进行控制
|    配置项    |    说明    |
|    ---     |
|    valid users = zhangsan[,lisi,@zhangsan]    |    <font color=red>合法用户，只有声明的用户和组才能登录指定的共享目录，用户名不在valid后面的登录不了    <br> @组名  指定组 </font>    |
|    write list = 用户名[,<font color=red>@组名</font>]    |    <font color=red>合法用户中，那些用户拥有写权限</font>    |
|    read only = yes    |    所有用户只读访问    
|    writable = yes    |    <font color=red>允许所有用户可以写入</font>    |

**访问要确保安装了`samba-client`包**

## 3. 访问方式

### 3.1 匿名访问
#### 3.1.1 查看服务器上的共享文件夹
```
smbclient -L 服务器IP   
```
例：
```
smbclient -L 192.168.1.100
```

#### 3.1.2 登录
```
smbclient IP地址
```
例：
```
smbclient //192.168.1.100
```

#### 3.1.3 登录并进入共享目录
```
smbclient  //服务器IP/共享文件夹名	
```
例：
```
smbclient //192.168.1.100/share
```

```
get 文件名下载
put 文件名上传
```

### 3.2 本地用户访问

#### 3.2.1 先要创建系统用户
```
usradd 用户名 
```

### 3.2.2 将该用户升级成samba用户
```
# 升级成samba用户设定密码
smbpasswd  -a  用户名
或者
pdbedit -a 用户名 添加samba用户 （新版）
```

查看有哪些samba用户
```
pdbedit -L 
```

查看共享目录
```
smbclient -U 用户名 -L  IP地址 
smbclient -U 用户名 //IP地址/文件名
```
注：windows下文件夹的缓存，虽然我已经将以前访问过的文件夹窗口都关掉了，但在后台还保持着它的连接，通过**`net use */del`**清空文件夹的缓存，把以前后台的连接全部中断。

### 3.3 访问控制
**<font color=red>在配置文件的[global]区域添加</font>**

```
hosts allow = 192.168.1.1,192.168.2. （全部拒绝，添加允许）
或者
hosts deny = 192.168.1.1    （全部允许，添加拒绝）
或者 
EXCEPT 添加例外
hosts allow = 192.168.1. EXCEPT 192.168.1.1
```

### 3.4 别名 

#### 3.4.1 在`/etc/smbusers`中添加
```
zhangsan = zhang 
```

#### 3.4.2 在主配置文件的global区域添加
```
username map = /etc/samba/smbusers
```
目录`training` 存放技术部资料 只允许技术部员工只读访问
目录`devel`存放项目开发数据，允许开发部员工读写访问

### 3.5 映射网络驱动器
windows
`工具` --> `映射网络驱动器`
Linux
```
mount -t cifs -o username=用户名,password=密码 //服务器地址/共享名  本地目录
或者
修改/etc/fstab文件 （开机自动挂载）
```