---
title: vim-surround插件
date: 2017-01-12 13:08:49
categories: vim
tags: vim插件 
---

Surround.vim is all about "surroundings": parentheses, brackets, quotes, XML tags, and more. The plugin provides mappings to easily delete, change and add such surroundings in pairs.

# 1. 修改 cs原符号要修改的符号

## 1.1 修改双引号为单引号 `cs"'`

It's easiest to explain with examples. Press `cs"'` inside

```
"Hello world!"
```
to change it to

```
'Hello world!'
```

## 1.2 修改单引号为html配对标签 `cs'<q>` 或者 `cs'<head>`

Now press cs'<q> to change it to

```
<q>Hello world!</q>

```

## 1.3 修改配对标签为其他字符 `cst要改成的字符`

To go full circle, press cst" to get
```
"hello world!"

```



# 2. 删除符号 `ds要删除的符号`

To remove the delimiters entirely, press ds".
```
Hello world!

```

# 3. 在文字的两侧添加字符 `ysiw要添加的字符`

Now with the cursor on "Hello", press ysiw] (iw is a text object).

```
[Hello] world!

```
Let's make that braces and add some space (use } instead of { for no space): cs]{

ysiw{ 有空格    cs]{ 有空格
ysiw} 无空格    cs]} 无空格
```
{ Hello } world!

```

# 4. 对整行操作 `yss`

Now wrap the entire line in parentheses with yssb or yss).

```
({ Hello } world!)

```

# 5. 删除符号 `ds删除的符号`

Revert to the original text: ds{ds)
```
hello world!

```
Emphasize hello: ysiw<em>

```
<em>Hello</em> world!

```

Finally, let's try out visual mode. Press a capital V (for linewise visual mode) followed by `S<p class="important">`.

```
<p class="important">
  <em>Hello</em> world!
</p>

```

This plugin is very powerful for HTML and XML editing, a niche which currently seems underfilled in Vim land. (As opposed to HTML/XML inserting, for which many plugins are available). Adding, changing, and removing pairs of tags simultaneously is a breeze.

The . command will work with ds, cs, and yss if you install repeat.vim.
