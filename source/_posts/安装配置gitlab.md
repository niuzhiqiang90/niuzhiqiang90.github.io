---
title: 安装配置gitlab
date: 2017-08-02 21:51:17
tags: gitlab
---

## 1. 安装
### 1.1 安装依赖
```
sudo apt-get install curl openssh-server ca-certificates postfix -y
```
### 1.2 下载安装包
[安装包下载地址](https://packages.gitlab.com/gitlab/gitlab-ce)

### 1.3 安装
```
sudo dpkg -i gitlab-ce_***.deb
```
至此，gitlab已经安装成功了。

## 2. 配置
### 2.1 修改配置文件
配置文件为`/etc/gitlab/gitlab.rb`
根据自己的需要修改该文件并保存。

### 2.2 生效配置
```
sudo gitlab-ctl reconfigure
```

### 2.3 测试
通过浏览器访问刚才配置的地址进行测试。  
至此，通过gitlab自带的nginx已经可以访问gitlab了。

---
如果我们服务器上已有`nginx`，那么我们想要让所有访问都通过已有的`nginx`作为总的出入口。  
## 3. 使用已有的nginx
[setting-the-nginx-listen-port](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/settings/nginx.md#setting-the-nginx-listen-port)

### 3.1 修改gitlab配置
修改如下内容

```
external_url 'https://gitlab.xxx.cn' # 将http修改为https
nginx['listen_addresses'] = ['127.0.0.1'] #这里修改为127.0.0.1
nginx['listen_port'] = 28080 

nginx['listen_https'] = false
nginx['proxy_set_headers'] = { 
  "X-Forwarded-Proto" => "https",
  "X-Forwarded-Ssl" => "on"
}
```

生效配置
```
sudo gitlab-ctl reconfigure
```

### 3.2 配置已有的nginx
#### 3.2.1 添加配置文件
```
cd opt/nginx-1.9/conf
vi nginx.conf
```
内容如下
```
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}

upstream gitlab.hosts {
    server 127.0.0.1:28080;
}

server {
    listen  80;
    server_name  gitlab.xxx.cn;
    return 301 https://gitlab.xxx.cn:443$request_uri;
}

server {
        #listen 80;
        listen 443 ssl;
        server_name gitlab.xxx.cn;
        ssl_certificate /home/USERNAME/opt/nginx-1.9/conf/etc/server.crt;
        ssl_certificate_key /home/USERNAME/opt/nginx-1.9/conf/etc/server.key;

        proxy_read_timeout      3600;
        proxy_connect_timeout   300;
        proxy_redirect          off;
        proxy_http_version 1.1;

        proxy_set_header Host $http_host_with_default;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header X-Forwarded-Ssl on;

        location / {
                proxy_redirect off;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_pass http://gitlab.hosts;
        }
    }
```

#### 3.2.2 启动nginx
```
cd opt/nginx-1.9/
sudo sbin/nginx -s reload
```

### 3.3 访问测试
通过浏览器访问`gitlab.xxx.cn`，就可以访问使用了。  
以上我们已经完成了`gitlab`的部署，并且配置了使用已有的`nginx`作为总的出入口。

---
如果我们之前已经有了`gitlab`，我们可能想要把之前的数据迁移进新的`gitlab`。  
## 4. 迁移数据
### 4.1 备份
在原有gitlab服务器上执行如下命令，备份gitlab
```
# 以下两条命令可以防止在备份的过程中有人提交代码，不能将整个gitlab停止，因为备份数据需要用到某些服务
sudo gitlab-ctl stop unicorn
sudo gitlab-ctl stop sidekiq
# 验证
sudo gitlab-ctl status
# 通过自带命令备份数据
sudo gitlab-rake gitlab:backup:create
# 备份之后将整个gitlab服务停止
sudo gitlab-ctl stop
# 将备份文件从gitlab/backups目录拷贝出来
cd /mnt/data/gitlab
sudo -s
cd backups
mv 1501564513_2017_08_01_gitlab_backup.tar /mnt/data
cd /mnt/data

# 修改用户属主属组
sudo chown USERNAME:USERNAME 1501564513_2017_08_01_gitlab_backup.tar
```

### 4.2 导入

#### 4.2.1 拷贝文件并修改权限
将上一步备份的数据放到新的服务器的`gitlab/backups`目录下
```
# 修改文件的属主属组
sudo chown git:git 1501564513_2017_08_01_gitlab_backup.tar
修改gitlab目录的属主属组
sudo chown git:git /mnt/data/gitlab
```

#### 4.2.2 导入数据
```
# 停掉如下两个服务
sudo gitlab-ctl stop unicorn
sudo gitlab-ctl stop sidekiq
# 验证
sudo gitlab-ctl status
# 导入数据
sudo gitlab-rake gitlab:backup:restore BACKUP=11501564513_2017_08_01
```

### 4.3 启动gitlab
```
sudo gitlab-ctl start
```
### 4.4 测试
之前的数据已经导入到新的`gitlab`服务器。  
现在可以通过原有的账号信息进行登陆验证。





