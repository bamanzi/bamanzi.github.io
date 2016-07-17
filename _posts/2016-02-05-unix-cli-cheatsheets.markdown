---
layout: post
title:  Cheat (以及tldr, bropages) - Unix命令用法备忘单
tags: cli,shell
---

[cheat](https://github.com/chrisallenlane/cheat) 是一个Unix命令行小工具，用来查询一些常用命令的惯用法（我们都知道，man page阅读起来太累了，常常是跳到最后去看 examples，但并不是所有man pages里面都有examples）。

举个例子，输入 `cheat tar` ，它就显示如下图所示的内容：

![](http://images2015.cnblogs.com/blog/163248/201602/163248-20160205144907288-1524355328.png)
（图来自: [Cheat - An Ultimate Command Line 'Cheat-Sheet' for Linux Beginners and Administrators](http://www.tecmint.com/cheat-command-line-cheat-sheet-for-linux-users/) )

### 安装与使用:

cheat 本身是用 python 写的，用 `pip install cheat` 即可完成安装。

数据文件： cheat 显示的内容来自于一堆文本文件，它称为cheatsheet，每个命令一个，目前自身带了[120个](https://github.com/chrisallenlane/cheat/tree/master/cheat/cheatsheets)常用命令的说明。你也可以自己编写，放在 `~/.cheat` 目录即可（如果觉得不错，可以通过Pull request乏匮给作者）。

### 类似工具:

**bash版本**: 我目前在用的其实是这个纯 bash 的实现: [jahendrie/cheat](https://github.com/jahendrie/cheat) 。它跟python这个版本的数据是兼容的。我是觉得python版本还依赖于docopt和pygments包，不如这个携带方便（只用拷贝`cheat.sh`和`~/.cheat` 就可以了）

[tldr](http://tldr-pages.github.io/): 一个用ruby写的类似工具，从GitHub加星数来看要更受欢迎一些（tldr: 7350; cheat: 2140 - 2016/02/05），并且有python/ruby/go等多个版本，并且还有android和iphone客户端。它与cheat的最大不同在于它的内容是从网上即时查询的，并不需要存储在本地（好像也不支持），[目前有250多个通用i命令、80多个linux命令和30多个osx命令的数据](https://github.com/tldr-pages/tldr/tree/master/pages )。

[Bropages](http://bropages.org/): 功能也差不多，跟 tldr 更像一点，因为内容是从网上即时查询的。从GitHub加星数来看要比cheat 差一个数量级（237 - 2016/02/05）。在代码库中没有看到数据文件，所以不知道能查到多少命令的帮助。


### 参考阅读

* [Cheat—— 给Linux初学者和管理员一个终极命令行"备忘单" - 博客 - 伯乐在线](http://blog.jobbole.com/97626/)
* [Cheat - An Ultimate Command Line 'Cheat-Sheet' for Linux Beginners and Administrators](http://www.tecmint.com/cheat-command-line-cheat-sheet-for-linux-users/)
* [有了 tldr，妈妈再也不用担心我记不住命令了 » Topics » 中国软件匠艺小组](https://codingstyle.cn/topics/26)
