---
title: git tag操作
date: 2017-03-22 13:17:19
tags: git
---

# 1. 显示本地tag
不带任何参数执行git tag，即可显示当前版本库里的tag列表
```
git tag
```
显示说明 `-n<num>` 显示最多num行的说明
```
git tag -n1
```
只显示名称和通配符相符的tag
```
git tag -l string*
```
# 2. 创建tag
## 2.1 创建轻量级里程碑
```
git tag <tagname> [<commitID>]
```
## 2.2 创建带说明的tag
```
git tag -a <tagname> [<commitID>]
或
git tag -m <msg> <tagname> [<commitID>] //直接通过-m参数提供tag说明
```

## 2.3 创建带GnuPG签名的tag
```
git tag -s <tagname> [<commitID>]
或
git tag -u <key-id>  <tagname> [<commitID>] //-u参数选择指定的私钥进行签名
```
创建tag需要输入tag的名称(<tagname>)和一个可选的提交ID(<commitID>)。<font color=red>如果没有提供提交ID，则基于头指针HEAD创建tag。</font>

# 3. 删除tag
本地
```
git tag -d <tagname> 
```
远程
```
git push origin --delete tag <tagname>
或
git tag -d <tagname> 
git push origin :refs/tags/<tagname>  #推送一个空tag到远程
```
# 4. 提交tag
提交单个tag
```
git push origin <tagname>
或
git push tag <tagname>
```
提交所有tag
```
git push --tags
或
git push origin --tags
```

