---
title: docker镜像
date: 2017-02-27 17:15:33
tags: docker
---

## 1. 获取镜像
```
sudo docker pull 镜像名
```
例：从Docker Hub仓库下载一个Ubuntu 12.04的操作系统的镜像
```
ubuntu@ubuntu:~$ sudo docker pull ubuntu:12.04
12.04: Pulling from ubuntu

8941035ae797: Pull complete 
191c4e1e4400: Pull complete 
fd04a9dc157f: Pull complete 
be08d59fa177: Pull complete 
ecf8c2069900: Pull complete 
8ea067ad7a27: Pull complete 
Digest: sha256:57a83f4b4f43f2d008c8ceaa9663a6f7ecd061fefa80da8f32e0f22c7b923390
Status: Downloaded newer image for ubuntu:12.04
```

## 2. 显示本地镜像
```
sudo docker images
```

```
ubuntu@ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
ubuntu              12.04               8ea067ad7a27        4 weeks ago         103.6 MB
```

## 3. 创建镜像

### 3.1 修改已有的镜像

- 使用下载的镜像启动一个容器
```
sudo docker run -t -i 镜像名 /bin/bash
```
例： 使用镜像ubuntu:12.04来启动一个容器
```
ubuntu@ubuntu:~$ sudo docker run -t -i ubuntu:12.04 /bin/bash
root@c2b59b70c1b7:/# pwd
/
root@f93c7537832d:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  selinux  srv  sys  tmp  usr  var
root@f93c7537832d:/# ps 
  PID TTY          TIME CMD
    1 ?        00:00:00 bash
   12 ?        00:00:00 ps
```
- 在容器中对安装软件
结束操作后，执行`exit`来退出。

- 提交更新后的副本
```
sudo docker commit -m "提交说明" -a 指定更新的用户信息 用来创建镜像的容器的ID 指定目标镜像的仓库名和tag信息
```
创建成功后会返回这个镜像的ID信息
例：
```
sudo docker commit -m "Add node" -a "username" aeee120a81fc testnode 

sudo docker images 查看新创建的镜像
sudo docker run -t -i testnode /bin/bash  使用新创建的镜像启动容器
```

### 3.2 使用Dockerfile来创建镜像
todo

### 3.3 从本地导入
```
sudo cat ubuntu-14.04.tar.gz | docker import - ubuntu:14.04
sudo docker images
```

### 3.4 上传镜像
用户在Docker Hub上完成注册后，可以可以通过`docker push`命令，把自己创建的镜像上传到docker的公共仓库来共享。
```
sudo docker push 镜像名
```

## 4. 存出镜像
```
sudo docker images
sudo docker save -o 保存文件名 镜像名
```

## 5. 载入镜像
可以使用`docker load`从导出的本地文件中再导入到本地镜像库。
```
sudo docker load --input ubuntu_14.04.tar
```
或
```
sudo docker load < ubuntu_14.04.tar
```
这将导入镜像以及相关的元数据信息（包括标签等）


## 6. 删除本地镜像
```
sudo docker rmi IMAGE_ID
```
