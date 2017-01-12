---
title: git分支操作
date: 2017-01-12 16:42:22
categories: git
tags: git
---

## 1 新建分支

### 1.1 新建本地分支

```
git checkout -b <branchName>
```

### 1.2 推送本地分支到远程

```
git push origin <localBranch>:<remoteBranch>
```



## 2 删除分支
### 2.1 删除本地分支

```
git branch -D <branchName>
```

### 2.2 删除远程分支
```
git push origin --delete <branchName>
```
