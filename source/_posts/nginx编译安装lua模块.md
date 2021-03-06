---
title: nginx编译安装lua模块
date: 2017-09-15 20:51:59
categories: nginx
tags: nginx
---

## 1. 获取nginx源代码
```
git clone https://github.com/nginx/nginx.git nginx-release
```

## 2. 下载安装LuaJIT
```
git clone https://github.com/LuaJIT/LuaJIT.git
cd LuaJIT
make 
make install
```

## 3. 下载lua-nginx-module
```
wget https://github.com/openresty/lua-nginx-module/archive/v0.10.7.tar.gz
tar zxvf v0.10.7.tar.gz
```
或者
```
git clone https://github.com/openresty/lua-nginx-module.git
```

## 4. 下载ngx_devel_kit
```
wget https://github.com/simpl/ngx_devel_kit/archive/v0.3.0.tar.gz
```
或者
```
git clone https://github.com/simpl/ngx_devel_kit.git
```

## 5. 编译安装nginx
```
cd ~/opt/nginx-release/
./configure --prefix=/home/USERNAME/opt/nginx-1.9 --with-ld-opt="-Wl,-rpath,/usr/local/lib" --add-module=/home/USERNAME/opt/nginx-module/lua-nginx-module-0.10.7 --add-module=/home/USERNAME/opt/nginx-module/ngx_devel_kit/ --with-http_stub_status_module --with-http_ssl_module --with-ipv6 --with-http_gzip_static_module --with-http_realip_module --with-http_flv_module --with-pcre-jit

make -j4
make install
```

## 6. 修改nginx配置
在http server区域内添加如下内容
```
location /hello_lua {
default_type 'text/plain';
content_by_lua 'ngx.say("hello, lua")';
}
```
重启nginx

## 7. 测试
浏览器访问`IP/hello_lua`显示
hello,lua  

nginx的lua模块安装完成。  


