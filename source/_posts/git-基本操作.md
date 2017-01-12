---
title: git 基本操作 
date: 2017-01-11 11:54:12
categories: git
tags: git
---

## 1 安装
- ubuntu
```
$ sudo apt-get install git gitk
```
- windows
[下载链接](https://www.git-scm.com/download/win)
- 其他系统
[点击这里](https://www.git-scm.com/downloads)


## 2 设置
- 全局设置（你的每次提交都会使用这些设置）  
```
$ git config --global user.name "姓名"
$ git config --global user.email "邮箱地址"
```
- 局部设置（只在当前项目中生效）
```
$ git config --local user.name "姓名"
$ git config --local user.email "邮箱地址"
```

## 3 更新本地代码
```
$ cd the/project/directory
$ git pull
```

## 4 提交代码
修改完成之后 提交修改的步骤如下
### 4.1 查看修改的文件
```
$ git status
```
### 4.2 查看修改的内容
```
$ git diff
```
### 4.3 从服务器上更新代码
```
$ git pull
```
### 4.4 将修改添加到本地仓库
```
$ git add 修改的文件名
```
```
$ git add . (当前目录下的所有修改)
```
例如 
```
git add a.txt c/b.txt
```
### 4.5 添加本次修改的描述
```
$ git commit -m "大致说明本次修改了什么"
```
### 4.6 将本地修改提交到服务器
```
$ git push
```
## 5 分支操作
### 5.1 查看所有分支
```
$ git branch -a
```
### 5.2 切换分支
- 第一次切换
```
$ git checkout -b *** origin/***
```
- 以后切换
```
$ git checkout ***
```
### 5.3 删除本地分支
```
$ git checkout the/other/branch
$ git branch -D the/delete/branch
```

** 补充 **  
[点击查看更详细的用法](https://mp.weixin.qq.com/s?__biz=MjM5NjQ4MjYwMQ==&mid=207476935&idx=1&sn=7e2df0fc83583b2970be033e295d595d&scene=4&srcid=0914UZRLJAd9vzLoflDXei0Y&pass_ticket=HC7otQjvd7%2FlrKPIl%2FUdQOZ4wjnTDDOzJXOZps0L2JZRnBVO0WgCnxedN7YVcvQi#rd)
