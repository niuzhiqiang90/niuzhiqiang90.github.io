---
title: 开源项目管理系统-zentao
date: 2017-08-18 21:38:11
tags:
---
## 1.下载zentao的安装包并解压

禅道的安装方式跟上一篇`《mediawiki》`安装方式类似。
禅道官网在[这里](https://www.zentao.net/)
```
mv mediawiki-1.27.3.tar.gz /mnt/data/
cd /mnt/data
unzip ZenTaoPMS.8.1.stable.zip
```
## 2. 进行域名解析
将要使用的域名(例如`zentao.xxx.cn`)解析到该服务器。

## 3. 配置nginx
```
server {
    listen  80;
    server_name  zentao.xxx.cn;
    return 301 https://zentao.xxx.cn:443$request_uri;
}

server {
        listen       443 ssl;
        server_name  zentao.xxx.cn;

        ssl_certificate /home/USERNAME/opt/nginx-1.9/conf/etc/zentao-server.crt;
        ssl_certificate_key /home/USERNAME/opt/nginx-1.9/conf/etc/zentao-server.key;

        root    /mnt/data/zentaopms;
        index index.php index.html index.htm;
        location ~ \.php$ {
             root   /mnt/data/zentaopms/www;
             fastcgi_pass 127.0.0.1:9000;
             fastcgi_index index.php;
             fastcgi_param  SCRIPT_FILENAME /scripts$fastcgi_script_name;
             include fastcgi.conf;
        }

        location / {
             root               /mnt/data/zentaopms/www;
             index index.php;
        }
}
```

## 4. 安装
通过浏览器访问`http://zentao.xxx.cn`，配置安装`zentao`即可。
确保mysql的账户密码信息正确。



