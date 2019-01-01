---
title: python字符串对齐
date: 2018-10-13 11:15:18
categories: python
tags: python
---

# python字符串对齐

```
title = "九九乘法口诀表"
```

## 1. 使用`ljust`,`rjust`,`center`方法
```
ljust(s, width, *args)
        ljust(s, width[, fillchar]) -> string
        
        Return a left-justified version of s, in a field of the
        specified width, padded with spaces as needed.  The string is
        never truncated.  If specified the fillchar is used instead of spaces.

rjust(s, width, *args)
        rjust(s, width[, fillchar]) -> string
        
        Return a right-justified version of s, in a field of the
        specified width, padded with spaces as needed.  The string is
        never truncated.  If specified the fillchar is used instead of spaces.

center(s, width, *args)
        center(s, width[, fillchar]) -> string
        
        Return a center version of s, in a field of the specified
        width. padded with spaces as needed.  The string is never
        truncated.  If specified the fillchar is used instead of spaces.
```

不指定填充字符默认空格
```
# 左对齐
print(title.ljust(100))
# 右对齐
print(title.rjust(100))
# 居中 
print(title.center(100))
```

指定填充字符为星号
```
print(title.ljust(100,'*'))
print(title.rjust(100,'*'))
print(title.center(100,'*'))

```

## 2. 使用`format`方法

`<` 左对齐
`>` 右对齐
`^` 居中对齐
不指定填充字符默认空格
```
print(format(title,"<100"));
print(format(title,">100"));
print(format(title,"^100"));

print('{:<100}'.format(title))
print('{:>100}'.format(title))
print('{:^100}'.format(title))

```

指定填充字符为星号
```
print(format(title,"*<100"));
print(format(title,"*>100"));
print(format(title,"*^100"));

print('{:*<100}'.format(title))
print('{:>*100}'.format(title))
print('{:^*100}'.format(title))

```

精度与类型f
```
print('{:.2f}'.format(3.1415926))
```

进制
```
print('{:b}'.format(13))
print('{:o}'.format(13))
print('{:d}'.format(13))
print('{:x}'.format(13))
```

用逗号作金额的分隔符
```
print('{:,}'.format(123456789))
```



