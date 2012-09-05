---
layout: post
title: "一日一Gem - Pry"
date: 2012-09-05 00:53
comments: true
categories: [gem, ruby]
---

本文主要介绍 Pry 的使用。

什么是 Pry
----------

Pry 是一个功能非常强大的工具，目前简单的理解为一个增强版的 irb 和强大的 debug
工具。

在了解 Pry 之前，先了解下什么是 REPL。

REPL 是 read-eval-print loop，是一种交互式的编程环境，可以用简单的 ruby
代码表示：

``` ruby test.rb
loop { p eval gets }
```

执行这个文件：

```irb 
1 + 1
2

puts "hello"
"hello"
nil
```

可以看到，使用 REPL 非常的简单优雅，可以马上看到执行结果以及返回值，
REPL 对于学习一门新的语言来说是非常直观的。


安装以及配置
------------

安装 pry：

``` bash
gem install pry pry-doc
```

跟 irb 以及其他 *nix 软件一样，你可以使用 rc 文件配置： `~/.pryc` 。

功能介绍
--------

打开一个终端，输入 `pry`，就开始了。

输入 `help` 可以查看可用的命令了，如果要获取某个命令的详细帮助，可以在后面加
`-h` 选项。

主要的命令有：

+ **cd**

  改变当前上下文，既是改变 `self` 对象。
  打开一个 `pry`，实际上我现在是在 `main` 对象上，在 pry 的 prompt
  上也可以看到当前 `self`。
  `cd Array` 之后，`self` 就变为 `Array` 了。

+ **ls**

  列出 `self` 的方法，实例变量，本地变量等。
  如果不指定对象就列出 `self` 的。

+ **show-doc**

  显示注释文档。

+ **show-method**

  显示代码。

+ **edit-method**

  打开编辑器，编辑指定的方法。
  可以直接修改在 `pry` 中定义的方法，免去重复录入的烦恼，
  或者在 debug 的过程中直接修改方法，方便调试。

+ **.shell-command**

  在 pry 中直接执行 shell command

+ **debug**

  在需要处加入 `binding.pry` 即可设置断点，可以用 pry-debugger 这个 gem
  来增强。

当然这里只是列出了一些比较常用的功能，
pry 最让我欣喜的一点就是通过 `cd` `ls` 这两个命令，
可以很好的了解 ruby 的对象模型，
对 ruby 元编程的学习也很有好处。

结合 rails 使用
-------------------

### rails console

既然 pry 这么好用，那么当然子 rails 中也要使用它咯。

``` ruby Gemfile
group :development do
  gem 'pry-rails'
end
```

打开 rails console，就可以在 rails 下用 pry 了。


### debug

断点处加上 `binding.pry`，`rails s` 启动服务，通过浏览器访问，
这时程序会在断点处停住，debug 完了后 `exit-all` 即可继续执行。

---------------------------------------

小结
------

pry 目前已经衍生出了很多的增强项目，如 pry-debugger，pry-remote 等等，
有时间再补充吧。

参考
------

+ [官网](http://pryrepl.org)

+ [pry gitub](https://github.com/pry/pry)

+ [railscasts](http://railscasts.com/episodes/280-pry-with-rails)

+ [xdite的blog](http://blog.xdite.net/posts/2012/08/12/pry-the-new-debugger/)

