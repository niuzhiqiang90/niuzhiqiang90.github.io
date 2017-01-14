---
title: git cherry-pick
date: 2017-01-14 13:06:14
categories: git
tags: git
---

## 1. 用法

```
$ git cherry-pick <commit id>
```

cherry-pick的作用是在当前分支上应用某一个提交，在合并的时候特别有用。
当前在 fixbug 分支上，做了3次修改并提交到本地

```
$ git branch
* fixbug
  master

$ git log --oneline 
43f50dd fix bug3
b24931f fix bug2
9ab09b7 fix bug1
```
这时候我发现 fix bug1 这个提交是不需要的，之需要将后2次合并进主分支即可。 可以这么操作:

```
$ git checkout master  #切换到master分支
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.

$ git cherry-pick 9ab09b7..43f50dd
[master 9cc5bca] fix bug2
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 b
[master 5a0704b] fix bug3
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 c

$ git log --oneline 
5a0704b fix bug3
9cc5bca fix bug2
c9e81c7 rename
```

可以看到，执行`check-pick`后， git 将指定的 commit <font color=red>(不含第一个，包含 最后一个)</font> 应用到了当前的 master 分支。执行完之后，可以删除掉 fixbug 分支。这样就可以很方便的从一个分支里面选取一部分提交合并到主分支里面去 了。当然同样也会遇到需要merge的情况。和merege一样操作即可。


## 2. 实例 

在本地 fixbug 分支上做了一个commit (09e827c96960780ebdee18040a4324de7fecba9b)， 如何把它放到 master 分支上？ 

办法之一： 使用 cherry-pick.  根据git 文档：
> Apply the changes introduced by some existing commits

就是对已经存在的commit 进行apply (可以理解为再次提交）
例如：
```
$ git checkout master
$ git cherry-pick 09e827c     # 这个09e827c号码，位于：
```

```
$ git log
commit 09e827c96960780ebdee18040a4324de7fecba9b
Author: niuzhiqiang <niuzhiqiang90@foxmail.com>
Date:   Fri Aug 12 10:52:32 2016 +0800

    add other

```

### 2.1 如果顺利，就会正常提交。结果：

```
$ git cherry-pick 9ab09b7..43f50dd
[master 9cc5bca] fix bug2
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 b
[master 5a0704b] fix 3
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 c
```

### 2.2 如果在cherry-pick的过程中出现了冲突

```
$ git cherry-pick 09e827c
error: could not apply 09e827c... add other
hint: after resolving the conflicts, mark the corrected paths
hint: with 'git add <paths>' or 'git rm <paths>'
hint: and commit the result with 'git commit'
```

#### 2.2.1 看哪些文件出现冲突

```
$ git status
On branch master
Your branch is ahead of 'origin/master' by 3 commits.
  (use "git push" to publish your local commits)

You are currently cherry-picking commit 09e827c.
  (fix conflicts and run "git cherry-pick --continue")
  (use "git cherry-pick --abort" to cancel the cherry-pick operation)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

        both modified:      b

no changes added to commit (use "git add" and/or "git commit -a")
```

```
$ git diff 
diff --cc b
index 7898192,6178079..0000000
--- a/b
+++ b/b
@@@ -1,1 -1,1 +1,5 @@@
++<<<<<<< HEAD
 +a
++=======
 +b
++>>>>>>> 09e827c... add other
```

#### 2.2.2 手动解决它

```
$ vi b 
<<<<<<< HEAD
a
=======
b
>>>>>>> 09e827c... add other
```

保存退出，手动提交

```
$ git add b
$ git commit -m "merge by handler"
$ git push
```
