---
layout: post
title: "ruby的$LOAD_PATH"
date: 2012-03-30 00:22
comments: true
categories: ruby
---

在ruby中，当你`require`一个文件的时候，
ruby去哪里寻找这个文件呢？
答案就是`$LOAD_PATH`了。
`$LOAD_PATH`还有另外一种perl风格的别名`$:`，
而且貌似这种写法更加常见，
虽然让人摸不着头脑就是了。

假设在`~/test`路径下，
有`foo.rb`和`bar.rb`，
在ruby 1.9.2 之前的版本里，
执行脚本的目录是加入到`$LOAD_PATH`里的，
所以你可以

``` ruby
# bar.rb
require 'foo'
```

ruby 1.9.2 之后的版本里，
出于安全的考虑，
不再包含当前目录，
所以上面的写法会出错，
所以相应的代码应该改为

``` ruby
# bar.rb
require './foo'
# or
require_relative 'foo'
```

其中`require './foo'`是当前目录相对文件的路径，
`require_relative`的意思是，
**包含这句代码的文件与目标文件的相对路径**。
下面是[stackoverflow][1]上的一段兼容1.9.2之前版本的代码

``` ruby
unless Kernel.respond_to?(:require_relative)
  module Kernel
    def require_relative(path)
      require File.join(File.dirname(caller[0]), path.to_str)
    end
  end
end
```

---------------------------------------

回到`$LOAD_PATH`，让我们看看它的值，

``` ruby
["/home/kun/.rvm/rubies/ruby-1.9.2-p290/lib/ruby/site_ruby/1.9.1",
 "/home/kun/.rvm/rubies/ruby-1.9.2-p290/lib/ruby/site_ruby/1.9.1/i686-linux",
 "/home/kun/.rvm/rubies/ruby-1.9.2-p290/lib/ruby/site_ruby",
 "/home/kun/.rvm/rubies/ruby-1.9.2-p290/lib/ruby/vendor_ruby/1.9.1",
 "/home/kun/.rvm/rubies/ruby-1.9.2-p290/lib/ruby/vendor_ruby/1.9.1/i686-linux",
 "/home/kun/.rvm/rubies/ruby-1.9.2-p290/lib/ruby/vendor_ruby",
 "/home/kun/.rvm/rubies/ruby-1.9.2-p290/lib/ruby/1.9.1",
 "/home/kun/.rvm/rubies/ruby-1.9.2-p290/lib/ruby/1.9.1/i686-linux"]
```

下面的方法可以将目录动态加入`$LOAD_PATH`

``` ruby
$:.unshift File.expand_path("../lib", __FILE__)
```

因为ruby以第一个找到的为准，
所以用unshift，可以提高些许效率。

---------------------------------------

最后，一点小发现，
rspec是会自动加载`.lib`和`.spec`的。

[1]: http://stackoverflow.com/questions/4333286/ruby-require-vs-require-relative-best-practice-to-workaround-running-in-both
