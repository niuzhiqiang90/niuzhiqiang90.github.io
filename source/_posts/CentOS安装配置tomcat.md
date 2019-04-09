---
title: CentOS安装配置tomcat
date: 2019-01-24 12:26:41
tags:
---

## 1. 更新系统软件
```
yum update
```

## 2. 下载并解压
下载tomcat，并解压至`/usr/local/`文件夹
```
tar -zxvf apache-tomcat-7.0.92.tar.gz -C /usr/local/
ln -s /usr/local/apache-tomcat-7.0.92 /usr/local/tomcat
```

## 3. 启动tomcat
```
/usr/local/tomcat/bin/startup.sh
```


## 4. 将tomcat配置为系统服务

### 4.1 添加`tomcat`文件
```
# cp catalina.sh /etc/init.d
# mv catalina.sh tomcat
```

### 4.2 修改tomcat文件
```
# vi /etc/init.d/tomcat
```
加入如下内容
``` 
#chkconfig:2345 10 90
#description:Tomcat service
CATALINA_HOME=/usr/local/tomcat
JAVA_HOME=/usr/local/jdk
```
<font color=red>注：上述代码必须加在`#OS specific support...`之上。</font>


### 4.3 修改权限
```
chmod +x /etc/init.d/tomcat
```

### 4.4 加入服务列表
```
chkconfig --add tomcat
```

### 4.5 验证是否加入列表
```
chkconfig --list tomcat
```

## 5. 测试
通过`service`命令来测试`tomcat`的启动与关闭  
启动
```
service tomcat start
```
启动后，通过浏览器访问`http://IP:8080/`可以看到tomcat主页


关闭
```
service tomcat stop
```



