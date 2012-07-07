---
layout: post
title: "Ack, Better Than Grep"
date: 2012-07-07 18:33
comments: true
categories: [vim, linux]
---

首先吐槽下ack 的域名，直接是[http://betterthangrep.com](http://betterthangrep.com) ，
这让`grep` 情何以勘。。。

## 什么是ack

`ack` 是一个类似于`grep` 并为程序员专门优化过的字符串搜索工具。

## 安装ack

```bash
# for ubuntu
sudo apt-get install ack-grep
```

因为已经有一个软件叫`ack` 了，所以可以：

```bash
alias ack="ack-grep"
```

## Why ack?

+ 快速
  
  `ack` 只搜索必需的文件。

+ 更好的搜索

  `ack` 默认忽略了`SVN` `Git` 以及其他版本控制系统的非源代码文件。

+ 为源代码搜索而设计

  不同于搜索文本的`grep`，`ack` 是专门搜索源代码的。

+ 高度的可移植性

  `ack` 是纯正的`perl` 脚本，可以在安装了`perl` 的`windows` 上运行。

+ 免费开源

## 小技巧

+ `--smart-case`，与vim的`smartcase` 类似，查找字符串全部小写着忽略大小写。

+ `-a` 查找全部文件，因为`ack`
  为提高速度而忽略掉一些文件，所以有时有必要加上这个选项，
  最常见的就是查找`ruby` 的某些文件，因为很多不能被识别为源代码文件。

+ `-Q` 省去表达式中转义的麻烦。

+ `--ruby` 只查找`ruby` 文件，类似的有`--perl`。

+ `show-types` 显示文件类型。

更多的可以`--help` 查看。

## ack.vim

`vim` 插件，可以于github [下载](https://github.com/mileszs/ack.vim.git)。

.vimrc 中加入

```vim
let g:ackprg="ack-grep -H --nocolor --nogroup --column"
```

我喜欢加入`--smart-case`。

之后在`vim` 中可以用`Ack` 等几个命令查找，结果会显示在`quickfix list` 中，
非常方便。
