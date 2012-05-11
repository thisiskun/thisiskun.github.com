---
layout: post
title: "linux折腾记"
date: 2012-05-09 11:35
comments: true
categories: linux
---

工欲善其事，必先利其器，想成为一个更好的code，开发环境当然是linux 了
（mac 没用过，穷逼买不起）。
趁着ubuntu 12.04发布之际，赶紧装上体验一把。
以下是折腾过程的记录，流水帐。

## 安装ubuntu

新硬盘一颗，刻碟，装上。
没有装windows 双系统，为什么呢？我会告诉你我很懒吗？

默认是gnome 桌面系统，还有unity，用起来感觉就是，别扭。
所以就安装了kde 桌面，

## 安装kde

``` bash
sudo apt-get install kubuntu-desktop
```

这也是我第一次接触kde 桌面，一开始感觉开机慢了，还不太习惯，而且由于双显卡驱动没装好，
特效开启不了，感觉很不爽啊。

## 正确安装双显卡驱动

我用的笔记本是hp 的dv3-4042tx，外观山寨苹果，不过工业设计吗，很多不合理的地方。
usb 接口只有两个，都在右边，而且靠的非常近。
另外要说的就是触摸板，一体化的触摸板，两个按键跟触摸区域无缝相连的，触摸区域大了没错，
不错按键手感就非常糟糕，还经常出现按键失灵的情况。

``` bash
lspci | grep VGA
```

``` bash
00:02.0 VGA compatible controller: Intel Corporation Core Processor Integrated Graphics Controller (rev 02)
01:00.0 VGA compatible controller: Advanced Micro Devices [AMD] nee ATI Manhattan [Mobility Radeon HD 5400 Series] (rev ff)
```

可以看到是intel 的集成显卡和ati独立显卡，装好了系统之后显示效果就一直不好，特效什么的也开不了，
所以就google 怎么正确的安装驱动，参考了这篇帖子。    
[http://forum.ubuntu.com.cn/viewtopic.php?f=42&t=350772](http://forum.ubuntu.com.cn/viewtopic.php?f=42&t=350772)

按照该帖的指导，并没有成功安装ati的闭源驱动，开机无法启动图形界面了，无奈只好卸载重启。
没想到重新开机，特效什么的全都出来了！柳暗花明又一村，有木有！

现在猜想，可能是

``` bash
sudo sh /usr/share/ati/fglrx-uninstall.sh
sudo apt-get remove --purge fglrx fglrx_* fglrx-amdcccle* fglrx-dev*
```

这里把ubuntu 安装的驱动给卸载了，所以就没有安装ati 显卡的驱动了，
而intel 的显卡则正常工作，特效什么的毫无压力，所以就正常了。

## 关闭独立显卡

现在集成显卡正常工作了，但是两块显卡其实都是通电的，温度有点高，而且不节能环保啊。
参照这里搞定。       
[http://linuxtoy.org/archives/how-to-use-vga-switcheroo-disable-video-card-linux-kms.html](http://linuxtoy.org/archives/how-to-use-vga-switcheroo-disable-video-card-linux-kms.html)

``` bash
cat /sys/kernel/debug/vgaswitcheroo/switch
```
输出大致如下：

``` bash
    0:IGD:+:Pwr:0000:00:02.0
    1:DIS: :Pwr:0000:01:00.0
```

两个都是Pwr。

编辑/etc/rc.local：

``` bash
chown yourname /sys/kernel/debug/vgaswitcheroo/switch
echo ON > /sys/kernel/debug/vgaswitcheroo/switch
echo IGD > /sys/kernel/debug/vgaswitcheroo/switch
echo OFF > /sys/kernel/debug/vgaswitcheroo/switch
exit 0
```

ok,重启，可以看到独立显卡一项是Off 的了。

## 设置fcitx

输入法使用的是fcitx，发现在konsole 和rekonq 下无法激活，
搜索了下，发现网上很多问这问题的，后来在官方wiki 上找到解决方法。

``` bash
sudo apt-get install qt4-qtconfig
sudo qtconfig
```

运行qtconfig (名称可能根据你的发行版有所不同)，选择第三个标签，确认fcitx 是在默认输入法组合框中中。

## oh-my-zsh

这个Ryan Bates 大神在railcasts 上介绍过了，默认配置就很好用了，
有特殊需求可以进一步折腾。

## ruby & rails

速配参考ruby-china 的[wiki 文档](http://ruby-china.org/wiki/install_ruby_guide)，
ruby当然是用rvm来装了，如果报`rvm is not a funciotn`，
把终端改为login-shell，并确保正确加载`~/,bash_profile`和`~/.bashrc`
另外

``` bash
sudo apt-get install libsqlite3-dev
sudo apt-get install nodejs
```

可以让你的第一个rails demo 跑起来。

## git & github

``` bash
git config --global user,name yourname
git config --global user.email youremail
git config --global core.editor vi
```

``` bash
ssh-keygen -t rsa -C "your_email@youremail.com"
```

然后将生成的ssh 公钥传到github 就好了。

## vim

``` bash
cd ~
git clone git@github.com:thisiskun/dotvim.git ~/.vim
ln -s ~/.vim/vimrc ~/.vimrc
ln -s ~/.vim/gvimrc ~/.gvimrc
mkdir ~/.vimbackup
mkdir ~/.vimundodir
cd ~/.vim
git submodule init
git submodule update
```
不fork 一下吗？

## 好软件

+ **Guake** 快捷下拉终端，超级方便啊
+ **krunner** alt-f2调出，感觉跟window的win+r差不多，这个软件很低调，找半天才找到名字
+ **amaorok** 音乐播放软件，用户体验很赞，logo超帅，不过貌似中文支持不太好
+ **dolphin** 文件管理软件
+ **konsole** 终端，多套配置可选，方便添加书签
+ **firefox** 这个说来话长

## the end

终于把linux 桌面环境配置的比较顺手了，折腾就是一个这样的过程：
每当你想，没辙了，其实，再转个弯就到了。
总体感觉，kde 桌面非常棒，完爆gonme。
之前有装过archlinux，感觉在知其所以然的情况下，用archlinux 应该会更好更方便，
配置简洁明了，滚动升级非常诱人，不过更适合熟手来折腾。
好了，折腾了那么多，是时候静下心来，干点正事了。

