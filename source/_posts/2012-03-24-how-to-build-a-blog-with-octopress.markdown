---
layout: post
title: "如何使用Octopress搭建blog"
date: 2012-03-24 17:44
comments: true
categories: [octopress, how-to]
---

[Octopress] [1]是一个静态的blog系统，
虽然副标题是“A blogging framework for hackers”，
其实只要稍微折腾下，搭建一个blog还是分分钟的事。
以下记录我是如何搭建这个blog的。

## 准备工作

+ 安装[ruby][8]，Octopress需要的是1.9.2版本，
  在ubuntu下使用[rvm][7]安装ruby是不二选择，可以方便的管理ruby以及rails的版本。
+ 安装[git][9]，在此不多描述。
+ 一个[Github][6]帐号，我是部署在github pages上的。
  什么！你还没有Github帐号？赶紧去注册个吧～～

## 安装Octopress

``` bash
git clone git://github.com/imathis/octopress.git my_blog
cd my_blog
gem install bundler
bundle install
rake install
```

## 基本配置

配置主要看`_config.yml`文件，一些基本的选项，注释里写得清晰易懂。
最sexy的一点就是第三方插件了。
Octopress是个静态的blog系统，使用[Disqus Comments][4]可以很方便的加入评论功能，
缺点是使用了javasctipt，对搜索引擎不太友好。
使用[Google Analytics][5]可以很方便的进行统计分析。

## 部署到Github Pages

假设你的Github帐号是foobar，那么新建一个repository，
name填`foobar.github.com`。

然后生成并部署。

``` bash
rake setup_github_pages
rake generate
rake deploy
```

`rake deploy`的时候会询问你repository url，
填入`git@github.com:foobar/foobar.github.com.git` 即可。

最后不要忘记提交到Github上。

```
git add .
git commit -m "your message"
git push origin source
```

现在访问`http://foobar.github.com`看看,是不是有了。

这里说下原来搞得我比较迷惑的地方。部署完毕后，
本地会有两个git branch，master跟source，
我们只需要提交source分支即可，master分支我想是本地备份之用。
另外提交到Github的master分支，是本地source下_deploy目录的master分支，
说起来可能比较绕口，
不过看看`Rakefile`应该会比较明白的。

当然Octopress也可以很方便的部署到gh-pages或者[heroku][10]，[官网][1]上都有详细的说明。

## Blogging basic 

Octopress会为你的blog生成`/atom.xml`或者
`/blog/catagories/<category>/atom.xml`。

### Blog posts

Octopress默认是使用markdown语法，simple and easy。

``` bash
rake new_post['your title'] 
```

这样就生成一篇新的post在你的`source/_post`目录下。

``` 
layout: post
title: "your title"
date: 2012-03-24 5:59
comments: true
categories:
```

你可以很方便的修改这些属性，比如multiple categories。

```
categories: octopress # one category

categories: [octopress, blog] # multiple catagories example 1

# multiple catagories example 2
catagories:
- octopress
- blog
```

如果你不想公开发布一篇post，
```
published: false
```
就可以了。

### New pages

添加一个新页面也是很简单的，这里我们来添加一个`about`页面好了。

```
rake new_page[about]
# creates /source/about/index.markdown
```

通过`root_url/about`就可以访问到这个页面了，下一步我们把他添加到导航栏。

``` html source/_includes/custom/navigation.html
<ul class="main-navigation">
  <li><a href="{{ root_url }}/">Blog</a></li>
  <li><a href="{{ root_url }}/blog/archives">Archives</a></li>
  <li><a href="{{ root_url }}/about">About</a></li>
</ul>
```

刷新下，`about`页面是不是出来了？

``` bash
rake generate
rake push
git push origin source
```

---------------------------------------

## 最后

总体而言，Octopress搭建、部署、配置和写blog都是十分的方便简单，一个字“爽”！

### 参考

+ [Octopress官网][1]
+ [markdown（英文）][2] 
+ [markdown（中文）][3]

[1]: http://octopress.org/ Octopress
[2]: http://daringfireball.net/projects/markdown/
[3]: http://markdown.tw/
[4]: http://thisiskun.disqus.com/ Disqus Comments
[5]: http://www.google.com/analytics/ Google Analytics
[6]: http://github.com Github
[7]: http://beginrescueend.com/ rvm
[8]: http://www.ruby-lang.org/en/ ruby
[9]: http://git-scm.com/ git
[10]: http://www.heroku.com/ heroku
