---
title: ubuntu安装为知笔记
date: 2017-01-20 15:38:17
tags: ubuntu
---

今天在ubunu上重新安装wiznote时，发现[官方](http://www.wiz.cn/wiznote-linux.html)提供的方法只能编译安装，无奈步骤繁琐。接着我尝试了之前的安装方法，发现会报错。

## 1.添加软件源来安装
ubuntu下可以通过软件源来进行安装，命令如下
```
$ sudo add-apt-repository ppa:wiznote-team
$ sudo apt-get update
$ sudo apt-get install wiznote
```
在更新时，会报`Hash Sum mismatch`
```
W: Failed to fetch http://ppa.launchpad.net/wiznote-team/ppa/ubuntu/dists/trusty/main/binary-amd64/Packages  Hash Sum mismatch
W: Failed to fetch http://ppa.launchpad.net/wiznote-team/ppa/ubuntu/dists/trusty/main/binary-i386/Packages  Hash Sum mismatch
W: Failed to fetch http://ppa.launchpad.net/wiznote-team/ppa/ubuntu/dists/trusty/main/i18n/Translation-en  Hash Sum mismatch
```
所以再执行install命令也会失败！！

## 2.直接从软件源上下载安装包

### 2.1 下载
执行如下命令时
```
$ sudo add-apt-repository ppa:wiznote-team
```
会输出如下日志
```
 The code is hosted here: https://github.com/WizTeam/WizQTClient
visit our homepage for detailed info: http://www.wiznote.cn/
Any questions and bugs report to: <email address hidden>
The binary name is WizNote. Please use WizNote to start program in cmd.
 More info: https://launchpad.net/~wiznote-team/+archive/ubuntu/ppa
Press [ENTER] to continue or ctrl-c to cancel adding it
```

打开[最后一个链接](https://launchpad.net/~wiznote-team/+archive/ubuntu/ppa)之后，点击 [View package details](https://launchpad.net/~wiznote-team/+archive/ubuntu/ppa/+packages) 就会跳转到如下页面
https://launchpad.net/~wiznote-team/+archive/ubuntu/ppa/+packages  点击其中的版本，下载相应的deb包。

### 2.2安装方法

- 方法1
双击下载文件，使用软件中心安装  
- 方法2
```
dpkg -i xxx.deb
```

