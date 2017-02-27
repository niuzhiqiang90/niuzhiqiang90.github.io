---
title: docker容器
date: 2017-02-27 17:26:49
tags: docker
---


## 1. 新建并启动容器
`docker run`
例：下面的命令输出一个“Hello World”并终止容器
```
sudo docker run ubuntu:14.04 /bin/echo 'Hello World'
Hello World
```
启动一个bash终端并允许交互
```
sudo docker run -t -i ubuntu:14.04 /bin/bash
root@3060c837bd2d:/#
```
`-t`让docker分配一个伪终端并绑定到容器的标准输入上，`-i`则让容器的标准输入保持打开。

## 2. 启动已终止的容器
```
sudo docker ps -a 找到容器ID
sudo docker start CONTAINER_ID
```

## 3. 守护态运行
```
$sudo docker run -d ubuntu:14.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"
```
在使用`-d`参数时,容器启动后会进入后台。某些时候需要进入容器进行操作,有很多种方法,包括使用`docker attach`命令或nsenter工具等。
```
ubuntu@ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
428e85fb5182        ubuntu:12.04        "/bin/sh -c 'while t   3 minutes ago       Up 3 minutes                            pensive_kirch       
ubuntu@ubuntu:~$ sudo docker attach 428e85fb5182
hello world
hello world
hello world
^Cubuntu@ubuntu:~$ 
ubuntu@ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
获取容器的输出信息，可以通过`docker log`命令。
```
sudo docker logs CONTAINER_ID
```

## 4. 终止容器
```
sudo docker stop CONTAINER_ID
```
处于终止态的容器可以通过`sudo docker start CONTAINER_ID`命令来重新启动。
`sudo docker restart CONTAINER_ID`命令可以重启一个运行态的容器。


## 5. 进入容器
```
sudo docker attach CONTAINER_ID
```
用attach命令有时候并不方便。当多个窗口同时attach到同一个容器的时候,所有窗口都会同步显示。当某个窗口因命令阻塞时,其他窗口也无法执行操作了。


## 6. 导出容器
```
sudo docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS           PORTS             NAMES
3060c837bd2d        ubuntu:14.04        "/bin/bash"              3 days ago          Up 6 hours                         nonghe

sudo docker export 3060c837bd2d > ubuntu.tar
```
这样导出容器快照到本地文件。

## 7. 导入容器快照
```
cat ubuntu.tar | sudo docker import - test/ubuntu:v1.0
sudo docker images
```
通过指定URL或者某个目录来导入
```
sudo docker import http://example.com/exampleimage.tgz example/imagerepo
```
既可以使用`docker load`来导入镜像存储文件到本地镜像库，也可以使用`docker import`来导入一个容器快照到本地镜像库。这两者的区别在于容器快照文件将丢弃所有的历史记录和元数据信息（即仅保持容器当时的快照状态），而镜像存储文件将保存完整记录，体积也要大。此外，从容器快照文件导入时可以重新指定标签等元数据信息。


## 8. 删除容器
处于终止状态的容器可以通过如下命令来删除
```
sudo docker rm CONTAINER_ID
```
如果要删除一个运行中的容器，可以添加`-f`参数。docker会发送SIGKILL信号给容器。

