---
title: ubuntu指定默认编辑器
date: 2017-02-07 13:28:17
categories: 
tags: ubuntu
---

ubuntu默认的编辑器为nano，例如执行如下命令时就会使用nano来打开
`sudo visudo`或者`git commit --amend`

如果想要设置成默认使用自己习惯的vi，可以执行如下命令进行选择

```
$ sudo update-alternatives --config editor 
There are 4 choices for the alternative editor (providing /usr/bin/editor).
  Selection    Path                Priority   Status
------------------------------------------------------------
* 0            /bin/nano            40        auto mode
  1            /bin/ed             -100       manual mode
  2            /bin/nano            40        manual mode
  3            /usr/bin/vim.basic   30        manual mode
  4            /usr/bin/vim.tiny    10        manual mode
Press enter to keep the current choice[*], or type selection number:

```
输入`3`就可以了。