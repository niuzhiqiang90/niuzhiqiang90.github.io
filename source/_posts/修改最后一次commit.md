---
title: 修改最后一次commit
date: 2017-01-11 21:42:43
categories: git
tags: git
---

## 命令

对于已经修改提交过的注释，如果需要修改，可以使用`git commit --amend`。

## example

在operation/bin里最新的提交就是 resolving the problem，我现在需要将其改为 resolving the problem for some errors

在framework/base下输入git commit –amend,就会进入一个文本编辑界面（如下）
```
     resolving the problem
     Change-Id: I7e7daffd2e8a5e65130629dc1c56dee5f5c4b5f7
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch shuai.lu_branch
# Changes to be committed:
#   (use "git reset HEAD^1 <file>..." to unstage)
#
#       modified:   operation/bin/alert
#
```
修改commit描述，然后保存退出，这样注释就修改了，再重新push就可以了。
