---
title: ubuntu安装powerline字体
date: 2017-01-22 14:25:10
categories: ubuntu
tags: vim插件
---

## 1.下载powerline字体和配置文件
```
$ wget https://github.com/powerline/powerline/raw/develop/font/PowerlineSymbols.otf
$ wget https://github.com/powerline/powerline/raw/develop/font/10-powerline-symbols.conf
```

## 2.将字体放到字体目录中
```
$ sudo mv ./PowerlineSymbols.otf /usr/local/share/fonts
```

## 3.更新字体缓存
```
$ fc-cache -vf /usr/share/fonts/
```

## 4.添加字体配置文件
```
$ sudo mv 10-powerline-symbols.conf /etc/fonts/conf.d/
```

这样vim中的powerline字体就能够正确显示了。
