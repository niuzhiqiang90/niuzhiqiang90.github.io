---
title: python字符编码
date: 2017-03-20 21:20:21
tags: python
---
## 1. ASSIC码
一共规定了128个字符的编码

### 1.1 ord函数
`ord('single-char')`

```
ord(...)
    ord(c) -> integer
    
    Return the integer ordinal of a one-character string.
```

```
>>> ord('a')
97
>>> type(ord('a'))
<type 'int'>
```

```
string = 'Hello World'
for i in string:
    print ord(i)
```

```
string = 'Hello World'
list_ascii = [ord(i) for i in string]
print list_ascii
```

### 1.2 chr函数
`chr(number)`

```
chr(...)
    chr(i) -> character
        
            Return a string of one character with ordinal i; 0 <= i < 256.
```



## 2. Unicode编码

16位 可以存下汉字

### 2.1 默认编码是ASCII
```
>>> a = 'hello'
>>> type(a)
<type 'str'>
>>> a
'hello'
```

### 2.2 加`u`则指定为Unicode
```
>>> a = u'hello'
>>> type(a)
<type 'unicode'>
>>> a
u'hello'
```


```
>>> b=u'你好'
>>> b
u'\u4f60\u597d'
>>> len(b)
2
```

## 3. UTF-8编码

```
# 转换成utf-8
>>> b.encode('utf-8')
'\xe4\xbd\xa0\xe5\xa5\xbd'


>>> b_utf8 = b.encode('utf-8')
>>> len(b_utf8)
6

```

## 4. Unicode和UTF-8的转换

### 4.1 Unicode >> UTF-8    

```
name.encode('utf-8')
```

```
>>> b=u'你好'
# Unicode转换成UTF-8 
>>> b_utf8=b.encode('utf-8')
```

### 4.2 UTF-8  >> Unicode

```
name_utf8.decode('utf-8')
```
```
>>> b_utf8
'\xe4\xbd\xa0\xe5\xa5\xbd'
>>> b_utf8.decode('utf-8')
u'\u4f60\u597d'
```

## 5. 指定程序的编码方式
程序如果有中文,需要指定编码方式
```
#_*_ coding:utf-8 _*_

name = u'中文'
print name
```