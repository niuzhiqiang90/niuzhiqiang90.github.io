---
title: git bisect
date: 2017-08-30 22:06:12
categories: git
tags: git
---

# git bisect
在提交了多次代码后，发现代码不能正确运行了，但是很确定的是某一个commit是好使的，比如昨天晚上或者上午的某次提交是可以正确运行的，这个时候可以通过`git bisect`来通过二分查找出问题的那次提交

## 1. 开始
```
git bisect start
```
## 2.标记当前的HEAD为bad
```
git bisect bad
```

## 3.标记最近一次可以正确运行的commit为good
```
git bisect good <commit-id> 
```
## 4.检查中间的那次提交状态是正确还是错误
- 如果是正确，标记为good
```
git bisect good
```
这时候，git会从这个good到最初标记的bad进行二分查找

- 如果是出错的，标记为bad
```
git bisect bad
```
这时候，git会从这个bad到最初标记的good位置进行二分查找


## 5.重复步骤4
即可找到最初的bad是在哪次commit不小心引入的。

## 6.结束
查找到引入bug的commit之后，回到最初的HEAD
```
git bisect reset
```








