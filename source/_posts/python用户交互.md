---
title: python用户交互
date: 2017-03-20 22:05:30
categories: python
tags: python
---

## 1. raw_input()
raw_input() 从标准输入中读取一个字符串
```
raw_input(...)
    raw_input([prompt]) -> string
    
    Read a string from standard input.  The trailing newline is stripped.
    If the user hits EOF (Unix: Ctl-D, Windows: Ctl-Z+Return), raise EOFError.
    On Unix, GNU readline is used if enabled.  The prompt string, if given,
    is printed without a trailing newline before reading.
```

例：
```
name = raw_input('Please input your name:')
age = raw_input('age:')
print "name: " % name
print "age:" % age
```

```
name = raw_input('Please input your name:')
age = raw_input('age:')
job = raw_input('Job:')
salary = raw_input('Salary:')

print '''
Personal information of %s:
   Name : %s
   Age  : %s     d只能输入数字 f只能输入浮点型
    Job : %s
  Salary: %s
''' %(name, name, age, job, salary)
```


## 2. input()
input()	输入的是什么就是什么格式

```
input(...)
    input([prompt]) -> value
    
    Equivalent to eval(raw_input(prompt)).
```

```
>>> info = "This var will be printed out ..."
>>> age = input('age:')
age:info
>>> print age
This var will be printed out ...
```
