---
title: python指定字符编码
date: 2017-02-07 09:37:14
tags: python
---

python指定字符编码的方式有多种
**编码格式是要写在解释器的下面的**


## 1.指定方法
```
#!/usr/bin/env python
# _*_ coding:utf-8 _*_
```

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
```

```
#!/usr/bin/env python
# coding:utf-8
```

## 2.使用场景
- 在python的程序中，如果有中文字符需要指定字符编码方式，无论是否是在注释中。
- 在数据库操作中，读取或者写入的内容中有汉字时。具体的方法如下:
```
#!/usr/bin/env python
# _*_ coding:utf-8 _*_

import sys
reload(sys)                                                                      
sys.setdefaultencoding('utf8')
```
