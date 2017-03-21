---
title: python使用和导入模块
date: 2017-03-20 21:47:06
tags: python
---

## 1. 导入模块的方法 

```
import moduleName 导入模块
From module import sayHi  从模块导入某个具体的方法
import moduleName as newName  给模块起别名
from sys import *   导入模块下的所有方法 
```

- 给模块起别名
```
import multiprocessing as multi
```


- 把模块下所有的方法导入
```
>>> from sys import *
>>> print path
['', '/usr/lib/python2.7', '/usr/lib/python2.7/plat-x86_64-linux-gnu', '/usr/lib/python2.7/lib-tk', '/usr/lib/python2.7/lib-old', '/usr/lib/python2.7/lib-dynload', '/usr/local/lib/python2.7/dist-packages', '/usr/lib/python2.7/dist-packages', '/usr/lib/python2.7/dist-packages/PILcompat', '/usr/lib/python2.7/dist-packages/gtk-2.0', '/usr/lib/pymodules/python2.7', '/usr/lib/python2.7/dist-packages/ubuntu-sso-client']
```

<font color=red>不建议使用`from sys import *` 可能被自定义的变量覆盖 调用时加上模块名 `sys.argv[2]` 可防止 </font>


## 2. 系统命令交互模块
### 2.1 os模块

```
>>> import os
>>> os.system('pwd')
/home/niuzhiqiang/Desktop
0
>>> cur_dir = os.system('pwd')
/home/xxx/Desktop
>>> print cur_dir
0

>>> os.popen('pwd')
<open file 'pwd', mode 'r' at 0x7f9017578c90>
>>> cur_dir = os.popen('pwd').read()
>>> print cur_dir
/home/xxx/Desktop
```
**例：循环列出目录下的内容**

```
>>> os.walk('/the/directory/')
>>> os.next()
>>> os.next()
```

### 2.2 commands模块
```
>>> import commands
>>> res = commands.getstatusoutput('pwd')
>>> print res
(0, '/home/xxx/Desktop')
>>> print res[1]
/home/xxx/Desktop
```

### 2.3 subprocess模块
todo



### 2.4 sys模块

取程序的参数
```
import sys
print sys.argv
print sys.argv[2]
```

```
from sys import argv
# 不需要使用`sys.`
print argv
```
