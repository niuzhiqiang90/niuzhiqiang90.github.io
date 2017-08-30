---
title: website enable ssl
date: 2017-08-09 20:33:10
tags: [nginx,ssl]
---

# website enable ssl

## 1. nginx enable ssl
网站启用ssl需要nginx ssl 模块的支持
### 1.1 查看nginx是否编译了ssl模块
```
nginx -V
```
### 1.2 编译ssl模块
```
cd nginx/src/dir
./configure --with-http_ssl_module
make && make install
```

## 2. 启用ssl
### 2.1 生成证书
```
sudo openssl genrsa -des3 -out server.key 2048
sudo openssl req -new -key server.key -out server.csr
sudo cp server.key server.key.org
sudo openssl rsa -in server.key.org -out server.key
sudo openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```

## 2.2 在server区域，指定证书位置
``` 
ssl_certificate /dir/server.crt;
ssl_certificate_key /dir/server.key;
```
## 2.3 修改监听的端口号
```
listen       443 ssl; 
server_name  ssl.test.cn; 
```

## 2.4 将http的访问redirect到https
在http区域添加如下内容
```
server {
       listen       80;
       server_name  ssl.test.cn;;
       return 301 https://ssl.test.cn:443$request_uri;
}
```

## 2.5 重启或者reloadnginx

## 2.6 测试
访问http://ssl.test.cn
已经会自动跳转到https://ssl.test.cn
