---
title: python文件处理
date: 2017-03-22 20:51:07
tags: python
---

## 1. 文件操作

file('文件名','操作符')
**省略操作符 默认为读**

| 模式 | 操作符 | 说明                                           |
| --   | --     | --                                             |
| 只读 | `r`    | 文件必须存在                                   |
| 只写 | `w`    | 文件不存在则创建，文件存在则清空文件内容       |
| 追加 | `a`    | 文件不存在则创建，文件存在则在文件末尾添加内容 |
| 读写 | `r+`   |                                                |
| 写读 | `w+`   |                                                |
| 追加 | `a+`   |                                                |

在python中，以什么样的方式出来文件是比较重要的，如果你
**以只读的方式打开文件，那你就不能写操作**
**以只写的方式打开文件，那你就不能读操作**
**以追加的方式打开文件，也应该是不能进行读操作的**
**<font color=red>操作完毕，不要忘记关闭文件</font>**

### 1.1 以只读方式打开文件
```
>>> f = file('test','r')  #f为该文件对象
>>> f.readline()
'hello world\n'
>>> f.readline()
'\n'
>>> f.readline()
'this is the second line\n'
>>> f.readline()
>>> f.close()

```

### 1.2 以只写方式打开文件
**如果文件存在并且有内容，则会把文件之前的内容清除；如果文件不存在。则会创建文件**
```
>>> f = file('test','w')
>>> f.read()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IOError: File not open for reading

>>> f.readline()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IOError: File not open for reading

>>> f.write('this is the newest line')
>>> f.close()
>>> f_read = file('test','r')
>>> f_read.read()
'this is the newest line'
>>> f_read.read()
''
```

### 1.3 以追加的方式打开文件
```
>>> f = file('test','a') 
>>> f.write('second line') 
>>> f.write('\nthird line')
>>>
```
这个时候，打开文件，刚才写入的内容并没有写入到文件里。
```
>>> f_read = file('test','r')
>>> f_read.readline()
'this is the newest line'
```
**文件在关闭的时候才会写入到文件里**或者**强制写入**
```
>>> f.flush()
```
追加模式也不可以读
```
>>> f.read()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IOError: File not open for reading

```

**拓展**
1. 去掉换行符 
`line.strip('换行符')`
省略换行符默认为空格

2. 把行分割 
`line.split('分隔符')`

## 2. 实例

### 2.1 读取`/etc/passwd`的内容
```
f = file('/etc/passwd')
for line in f.readlines(): #循环文件的每一行 
    line = line.strip('\n').split(':') 
    print line
```
得到的结果如下**一个列表**：
```
['root', 'x', '0', '0', 'root', '/root', '/bin/bash']
['daemon', 'x', '1', '1', 'daemon', '/usr/sbin', '/usr/sbin/nologin']
['bin', 'x', '2', '2', 'bin', '/bin', '/usr/sbin/nologin']
['sys', 'x', '3', '3', 'sys', '/dev', '/usr/sbin/nologin']
['sync', 'x', '4', '65534', 'sync', '/bin', '/bin/sync']
['games', 'x', '5', '60', 'games', '/usr/games', '/usr/sbin/nologin']
['man', 'x', '6', '12', 'man', '/var/cache/man', '/usr/sbin/nologin']

```
打印第1列，第2列
```
print line[0],line[1]
```

### 2.2 写文件
语言本身不存在文件保护的设置，避免多个程序对同一个文件进行操作，需要自己来完成。
```
>>> f.closed
False
```
**如果`f.closed`值为False,说明文件已经被打开**

显示文件的字符集，默认为ASCII
```
>>> f.encoding
>>> print f.encoding
None
```

```
>>> f = file('test_coding','w') 
>>> f.write(u'测试文件编码') 
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-5: ordinal not in range(128)
>>> f.write(u'测试文件编码'.encode('utf-8'))
>>> f.flush()
```

读到末尾，`next()`会报错
```
>>> f = file('test_coding','r') 
>>> f.readline()
''
>>> f.next()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
>>> 
```


## 3. 文件操作相关方法
readline是通过next实现的

| 方法            | 说明                           |
| ---             | ---                            |
| f.readlines()   | 以列表的形式读出文件           |
| f.read()        | 以字符串的形式，一次性读出文件 |
| f.seek()        | 跳到文件的指定位置             |
| f.tell()        | 当前位置                       |
| f.truncate(100) | 从头开始截断文件               |
| f.writelines()  | 多行写                         |
| f.xreadlines()  | 逐行读                         |

