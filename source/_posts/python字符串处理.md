---
title: python字符串处理
date: 2017-03-23 21:49:45
categories: python
tags: python
---

## 1. 字符串处理函数

| 函数                              | 说明                                                         |
| --                                | --                                                           |
| s.find(substring,[start[,end]])   | 查找子字符串，可以指定开始结束的位置，返回索引值，否则返回-1 |
| s.rfind(substring,[start[,end]])  | 反向查找字符串                                               |
| s.index(substring,[start[,end]])  | 同find，找不到产生ValueError异常                             |
| s.rindex(substring,[start[,end]]) | 同反向查找                                                   |
| s.count(substring,[start[,end]])  | 返回找到子串的个数                                           |
| s.lowercase()                     | 所有字符转换成小写                                           |
| s.capitalize()                    | 首字母转换成大写                                             |
| s.lower()                         | 所有字符转换成小写                                           |
| s.upper()                         | 所有字符转换成大写                                           |
| s.swapcase()                      | 大小写互换                                                   |
| s.split(str,'')                   | 将string转换成list，以空格分割                               |
| s.join(list,'')                   | 将list转换成string,以空格连接                                |
**处理字符串的内置函数**

| 函数名               | 说明                        |
| --                   | --                          |
| len(str)             | 串长度                      |
| cmp('first_str',str) | 字符串比较，第一个大，返回1 |
| max('string')        | 寻找字符串中最大的字符      |
| min('string')        | 寻找字符串中最小的字符      |

## 2. 实例

### 2.1 查找子字符串
`s.find()`

```
>>> string = "this is the test string"
>>> string.find('test')
12
```

### 2.2 查找子字符串
`s.index()`
找不到就报错，找到就返回索引
```
>>> string = "this is the test string"
>>> string.index('error')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: substring not found
>>> string.index('test')
12
```

### 2.3 首字母转换成大写
`s.capitalize()`

```
>>> string = "this is the test string"
>>> string.capitalize()
'This is the test string'
```

### 2.4 字符串转换成大写
`s.upper()`

```
>>> string.upper()
'THIS IS THE TEST STRING'
```


### 2.5 字符串转换成小写
`s.lower()`

```
>>> string.lower()
'this is the test string'
```

### 2.6 大小写互换
`s.swapcase()`

```
>>> string += "THIS IS CAP"
>>> string
'this is the test stringTHIS IS CAP'
>>> string.swapcase()
'THIS IS THE TEST STRINGthis is cap'
```

### 2.7 将string转换为list
`s.split()`
默认为空格，可以指定分隔符
```
>>> string.split()
['this', 'is', 'the', 'test', 'stringTHIS', 'IS', 'CAP']
```

### 2.8 将list转换为string
`s.join()`
```
>>> '|'.join(string.split())
'this|is|the|test|stringTHIS|IS|CAP'
```

**拓展**
`+=`和`.join`实现的效果是一样的，但是`+=`每次都会新开辟空间，但是`.join`不用
例：
```
>>> a = 'the'
>>> a += ' test'
>>> a += ' is'
>>> a += ' end'
>>> a
'the test is end'

>>> ''.join(a)
'the test is end'
```



