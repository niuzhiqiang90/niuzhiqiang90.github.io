---
title: LNMP环境部署
date: 2017-08-17 20:36:39
tags:
---
## 0. 源由
由于公司需要搭建一些项目，例如`zentao`和`mediawiki`，依赖`LNMP`环境。

## 1. 安装Nginx 

```
# 安装依赖
sudo aptitude install libssl-dev libpcre3 libpcre3-dev zlib1g-dev  
cd /home/USERNAME/opt
# clone nginx源代码
git clone https://github.com/nginx/nginx.git nginx-releases
cd ./nginx-releases  
git checkout -b release-1.9 release-1.9
./auto/configure --prefix=../nginx-1.9  --with-http_ssl_module
make -j4  
make install  
```


## 2. 安装MySQL
[Download link](https://cdn.mysql.com//Downloads/MySQL-5.5/mysql-5.5.57-linux-glibc2.12-x86_64.tar.gz)  
[Installing MySQL on Unix/Linux Using Generic Binaries](https://dev.mysql.com/doc/refman/5.5/en/binary-installation.html)

### 2.1 安装依赖库
```
shell> apt-get install libaio1 # install library
```

### 2.2 安装mysql
```
shell> groupadd mysql
shell> useradd -r -g mysql -s /bin/false mysql
shell> cd /home/USERNAME/opt
shell> tar zxvf mysql-5.5.57-linux-glibc2.12-x86_64.tar.gz
shell> mv mysql-5.5.57-linux-glibc2.12-x86_64 mysql-5.5.57
shell> cd mysql-5.5.57
shell> chown -R mysql .
shell> chgrp -R mysql .
shell> scripts/mysql_install_db --user=mysql
shell> chown -R root .
shell> chown -R mysql data
# Next command is optional
shell> cp support-files/my-medium.cnf /etc/my.cnf
shell> bin/mysqld_safe --user=mysql &
# Next command is optional
shell> cp support-files/mysql.server /etc/init.d/mysql.server
```
到此mysql已经安装完成。


## 3. 安装PHP
[PHP 5.6.31下载链接](http://us1.php.net/distributions/php-5.6.31.tar.gz)

### 3.1 解压安装包
```
tar -zxvf php-5.6.31.tar.gz
```
### 3.2 解决依赖
```
sudo apt-get install libxml2-dev
```
```
sudo apt-get install libpng12-0 libpng12-dev
```
### 3.3 编译
```
cd /home/USERNAME/opt/php-5.6.31.src
./configure --prefix=/home/USERNAME/opt/php-5.6.31 --with-gd --with-zlib --with-mysql=/home/USERNAME/opt/mysql-5.5.57 --with-config-file-path=/home/USERNAME/opt/php-5.6.31 --enable-mbstring --enable-fpm
```
### 3.4 安装
```
make && make install
```
### 3.5 拷贝配置文件到指定位置
```
cd /home/USERNAME/opt/php-5.6.31
cp -p /home/USERNAME/opt/php-5.6.31/php.ini-development ./php.ini

cd /home/USERNAME/opt/php-5.6.31/etc/
cp -p php-fpm.conf.default php-fpm.conf
```

### 3.6 修改php-fpm.conf
```
vi php-fpm.conf
```
修改为如下内容
```
pid=run/php-fpm.pid
user = USERNAME
group = USERNAME
pm.max_children = 50
pm.start_servers = 20
pm.min_spare_servers = 5
pm.max_spare_servers = 35
```

### 3.7 安装`pdo`模块
```
cd /home/USERNAME/opt/php-5.6.31.src/ext/pdo
/home/USERNAME/opt/php-5.6.31/bin/phpize
./configure --with-php-config=/home/USERNAME/opt/php-5.6.31/bin/php-config
make & make install
```

### 3.8 安装`pdo_mysql`模块
``` 
cd /home/USERNAME/opt/php-5.6.31.src/ext/pdo_mysql
/home/USERNAME/opt/php-5.6.31/bin/phpize
./configure --with-php-config=/home/USERNAME/opt/php5/bin/php-config --with-pdo-mysql=/home/USERNAME/opt/mysql-5.5.57/
```

### 3.9 安装`openssl`模块
```
cd /home/USERNAME/opt/php-5.6.31.src/ext/openssl
cp -p config0.m4 config.m4
/home/USERNAME/opt/php-5.6.31/bin/phpize
ln -s /usr/lib/x86_64-linux-gnu/libssl.so  /usr/lib
./configure --with-openssl --with-php-config=/home/USERNAME/opt/php-5.6.31/bin/php-config
make
make install
```
`--with-php-config`指定配置
`--with-pdo-MySQL`指定mysql数据库的安装目录

安装完成会出现如下内容
```
Installing shared extensions:	/home/USERNAME/opt/php-5.6.31/lib/php/extensions/no-debug-non-zts-20131226/
```
编辑php.ini文件，将出现的内容添加到配置文件里
```
extension=/home/USERNAME/opt/php-5.6.31/lib/php/extensions/no-debug-non-zts-20131226/pdo_mysql.so
extension=/home/USERNAME/opt/php-5.6.31/lib/php/extensions/no-debug-non-zts-20131226/openssl.so
```
### 3.10 启动fpm
```
cd /home/USERNAME/opt/php-5.6.31/sbin
sudo php-fpm
```

## 4. 编辑测试文件并测试
```
cd /home/USERNAME/opt/nginx-1.9/html/
echo "<?php
phpinfo();
?>
```
修改nginx配置文件
```
server {
	listen  80;
	server_name localhost;
	
	location ~ \.php$ {
             root   /home/USERNAME/opt/nginx-1.9/html;
             fastcgi_pass 127.0.0.1:9000;
             fastcgi_index index.php;
             fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
             include fastcgi.conf;
          }

}
```
通过curl访问`localhost/index.php`即可查看到当前环境的php信息。


