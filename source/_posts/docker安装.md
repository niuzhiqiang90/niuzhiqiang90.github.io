---
title: docker安装
date: 2017-02-27 17:03:14
tags: docker
---

## 1. ubuntu通过Docker源安装最新版本
### 1.1 安装依赖
要安装最新的Docker版本，首先需要安装apt-transport-https支持，之后通过添加源来安装。
```
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates
```
### 1.2 添加GPG key
```
sudo apt-key adv \
               --keyserver hkp://ha.pool.sks-keyservers.net:80 \
               --recv-keys 58118E89F3A912897C070ADBF76221572C52609D 
               
```
### 1.3 添加对应的源
源列表
| Ubuntu version      | Repository                                                 |
| ---                 |
| Precise 12.04 (LTS) | deb https://apt.dockerproject.org/repo ubuntu-precise main |
| Trusty 14.04 (LTS)  | deb https://apt.dockerproject.org/repo ubuntu-trusty main  |
| Wily 15.10          | deb https://apt.dockerproject.org/repo ubuntu-wily main    |
| Xenial 16.04 (LTS)  | deb https://apt.dockerproject.org/repo ubuntu-xenial main  |

```
echo "<REPO>" | sudo tee /etc/apt/sources.list.d/docker.list
```
### 1.4 更新apt包的index
```
sudo apt-get update
```
### 1.5  安装docker
```
sudo apt-get install docker-engine
sudo service docker start
sudo docker run hello-world 
```

## 2. CentOS安装

### 2.1 更新包
```
sudo yum update
```
### 2.2 添加yum源
```
sudo tee /etc/yum.repos.d/docker.repo << EOF
[DockerRepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/7/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF
```
### 2.3 安装docker包
```
sudo yum -y install docker-engine
```
### 2.4 开启启动
```
sudo systemctl enable docker.service
```
### 2.5 启动服务
```
sudo systemctl start docker
```
## 3. 验证docker是否安装成功
```
sudo docker run --rm hello-world

Unable to find image 'hello-world:latest' locally
 latest: Pulling from library/hello-world
 c04b14da8d14: Pull complete
 Digest: sha256:0256e8a36e2070f7bf2d0b0763dbabdd67798512411de4cdcf9431a1feb60fd9
 Status: Downloaded newer image for hello-world:latest

 Hello from Docker!
 This message shows that your installation appears to be working correctly.

 To generate this message, Docker took the following steps:
  1. The Docker client contacted the Docker daemon.
  2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
  3. The Docker daemon created a new container from that image which runs the
     executable that produces the output you are currently reading.
  4. The Docker daemon streamed that output to the Docker client, which sent it
     to your terminal.

 To try something more ambitious, you can run an Ubuntu container with:
  $ docker run -it ubuntu bash

 Share images, automate workflows, and more with a free Docker Hub account:
  https://hub.docker.com

 For more examples and ideas, visit:
  https://docs.docker.com/engine/userguide/
```


[docker on github](https://github.com/docker/docker)  
[docker install mothed](https://docs.docker.com/engine/installation/)
