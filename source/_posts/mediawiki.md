---
title: 'mediawiki'
date: 2017-08-18 21:08:11
tags:
---

## 1. 准备
`mediawiki`依赖`LNMP`环境，所以在部署前，需要首先搭建`LNMP`环境，参考上一篇。

## 2. 下载mediawiki并解压
[下载链接](https://releases.wikimedia.org/mediawiki/1.27/mediawiki-1.27.3.tar.gz)  
```
wget https://releases.wikimedia.org/mediawiki/1.27/mediawiki-1.27.3.tar.gz
mv mediawiki-1.27.3.tar.gz /mnt/data/
cd /mnt/data
tar -zxvf mediawiki-1.27.3.tar.gz
```
## 3. 进行域名解析
将要使用的域名(例如`mediawiki.xxx.cn`)解析到该服务器

## 4. 配置nginx
```
server {
    listen  80;
    server_name  mediawiki.xxx.cn;
    return 301 https://mediawiki.xxx.cn:443$request_uri;
}

server {
        listen  443 ssl;
        server_name     mediawiki.xxx.cn;

        ssl_certificate /home/USERNAME/opt/nginx-1.9/conf/etc/wiki-server.crt;
        ssl_certificate_key /home/USERNAME/opt/nginx-1.9/conf/etc/wiki-server.key;


        root    /mnt/data/mediawiki-1.27.3;
        #index  index.php index.html index.htm;

        location ~ \.php$ {
             root               /mnt/data/mediawiki-1.27.3;
             fastcgi_pass       127.0.0.1:9000;
             fastcgi_index      index.php;
             fastcgi_param      SCRIPT_FILENAME /scripts$fastcgi_script_name;
             include            fastcgi.conf;
        }

        location / {            
             root               /mnt/data/mediawiki-1.27.3;
             index index.php;
        }
    }
```

## 5. 安装
通过浏览器访问`mediawiki.xxx.cn`来进行安装即可。
其中要注意正确设置连接数据库的用户名密码。