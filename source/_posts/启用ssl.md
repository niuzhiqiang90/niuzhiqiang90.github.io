---
title: 启用ssl
date: 2017-08-09 20:33:10
tags: [nginx,ssl]
---

## 1. nginx enable ssl
网站启用ssl需要nginx ssl模块的支持

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

### 2.2 在server区域，指定证书位置
``` 
ssl_certificate /path/server.crt;
ssl_certificate_key /path/server.key;
```

### 2.3 启用ssl
修改nginx配置，启用ssl
```
listen       443 ssl; 
server_name  ssl.test.cn; 
```

### 2.4 将http的访问redirect到https
在http区域添加如下内容
```
server {
       listen       80;
       server_name  ssl.test.cn;;
       return 301 https://ssl.test.cn:443$request_uri;
}
```

## 3. 生效配置
重启或者`reload` nginx

## 4. 测试
浏览器访问http://ssl.test.cn，测试验证。