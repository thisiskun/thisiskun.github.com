---
layout: post
title: "linux keyboard tricks"
date: 2012-12-24 18:24
comments: true
categories: linux
---

在此记录 linux 命令行的一些实用小技巧，使用的环境是 zsh + oh-my-zsh，
对 bash 部分不兼容。

## 命令行输入

### 移动和编辑

+ `ctrl_a` 跳到行首
+ `ctrl_e` 跳到行末
+ `ctrl_l` 清屏
+ `ctrl_u` 清除整行
+ `ctrl_k` 光标后内容
+ `ctrl_w` 清除光标前一个单词
+ `alt_d`  清除光标后一个单词
+ `alt_t`  光标前两个单词互换位置

**实用例子**

`alt_d` 相比 `ctrl_w`
这个命令用的应该比较少，不过如果一长串命令前面不小心加上了
`sudo`，最快的方式就是
`ctrl_a` + `alt_d` 再 enter

### 历史相关

+ `!!` 上一个命令
+ `!!-n` 倒数第n个命令
+ `alt_.` 历史命令的最后一个单词
+ `alt_n` `alt_.` 历史命令的倒数第n个单词
+ `!*` 上个命令的全部参数
+ `ctrl_p` 向前导航历史命令
+ `ctrl_n` 向后导航历史命令
+ `ctrl_r` 历史命令检索，按下后输入之前命令的片段，动态补全，回车确认，`ctrl_c`
  取消
+ `foobar key_up` 输入 `foobar` 后按向上键可以搜索 `foobar` 开头的命令
+ `foobar key_down` 同上

**注意** 截至本文发布时，zsh 要使用 key_up key_down 功能必须在 `.zshenv`
中加入：

``` sh
DEBIAN_PREVENT_KEYBOARD_CHANGES=yes
```

**实用例子**

`sudo !!` 使用时遇到无权限时可快速使用 sudo

### 表达式

+ 花括号表达式
+ 算数表达式
+ 命令表达式
+ 双引号和单引号

**实用例子**

+ 重命名，超好用有没有

`mv afuckinglongpath.{css,scss}`

+ 跳转命令所在目录

``` sh
cd `whick rake`
```

+ 双引号转义，单引号不转义

``` sh
echo "$PATH"
echo '$PATH'
```

## 导航

`cd -` 跳转上一个目录

`d` 最近的目录

`cd +n` 跳转上面列出的目录，直接输入 `n` 也可以

## 其他

多用 alias，你会发现 oh-my-zsh 里面很多插件其实就是 alias。

当然，不要忘了我们的 `tab`。

------------------------------

## 参考

+ [the linux command](http://linuxcommand.org)
