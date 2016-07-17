---
layout: post
tags: putty, scp, sftp
title: 如何针对当前PuTTY会话上传/下载文件
---

如果你是个PuTTY重度用户，在使用ssh连上一个远端机器工作了好一阵子后，发现自己需要对 *当前会话* 上传/下载文件，要怎样才能简单快捷呢？


## 最简单的方式

最简单的方法: 安装[WinSCP](http://winscp.net )或者[Filezilla](http://filezilla-project.org/ ), 启动该程序，然后自己输入输入主机名、端口、用户名、密码登录，然后在putty里面用`pwd`命令看看当前目录，再在WinSCP/Filezilla中跳转到那个目录去，再传送文件．

WinSCP界面(commander风格．另外还支持explorer风格):
![winscp main window](https://www.diigo.com/file/image/braraszdcaqpeqqbzcdopaqcr/Screenshots+%3A%3A+WinSCP.jpg )

**优点**：上手容易，没有额外的学习负担

**缺点**：

  - 繁琐（为什么还要我再输入一遍主机名、端口名、登录用户名这些？为什么不能复用当前的ssh连接呢？）;
  - 而且有多个PuTTY连接时，这边要上传，那边要下载，还得搞明白PuTTY会话与WinSCP/Filezilla窗口的对应关系，否则一不小心就弄混了。

## 方法一: 用KiTTY + WinSCP/kscp

我们可以用[KiTTY](http://kitty.9bis.com/ )(已被蔷，不过可以从这里下载:<http://www.filehorse.com/download-kitty/>, 如果需要带有kageant/kscp的版本，可从<https://chocolatey.org/packages/kitty>下载 )来替代PuTTY，因为它支持 **一键接通winscp和pscp**（不再需要你输入机器名、用户名、密码）。当然，你首先得装这两个东西（可下载便携版本放到PATH内）

在KiTTY中，这两个操作均是在窗口菜单上发起（点击kitty窗口左上角图标或者在标题栏点右键）![kitty window menu](https://www.diigo.com/file/image/braraszdcaqodasdzcdoosqdq/kitty+window+menu.jpg ) (此图裁剪自: [Putty You Go Away - Kitty Takes its Place - KossBoss](http://www.kossboss.com/windows---putty-you-go-away---kitty-takes-its-place )

如果你只是需要上传一个文件，可以在kitty的窗口菜单上发起`Send with pscp`命令，

如果你需要下载文件，或者需要传送多个文件甚至整个目录，那么可以在kitty的窗口菜单上发起`Start WinSCP`，kitty与WinSCP结合的优点是你不用再输入任何信息。

**优点**: 从KiTTY打开WinSCP时，你不用再输入主机名/端口/用户名/密码。

**缺点**：

  - 如果你一直用得是PuTTY，那么得迁移到KiTTY，这里有迁移成本：KiTTY虽然兼容PuTTY的功能，但session保存在注册表的不同位置（第一次启动KiTTY时它会导入PuTTY的sessions，但后来再用PuTTY增加删除修改的session并不自动刷新到KiTTY里面去）。所以想使用这个kitty的这个功能意味着你要承担**迁移**成本（putty的生态要好一些，有不少第三方工具，但这些第三方工具能否工作于KiTTY就不一定了）
  - 另外打开WinSCP后仍然需要手工转到kitty里面的当前工作目录，这一点上不如下面的zmodem方便

参考： [pscp and WinSCP integration - KiTTY](http://www.9bis.net/kitty/index.php?page=pscp%20and%20WinSCP%20integration ) 注意：被蔷

## 方法二：用SuperPuTTY的sftp界面

[SuperPuTTY](https://github.com/jimradford/superputty )是一个PuTTY的多标签页管理程序，但它同时还提供了文件传输界面．

 []  图
 
**优点**：
 
 - 可以不必迁移到kitty（不过，SuperPuTTY也支持kitty）
 - 功能直接提供在SuperPuTTY内，没有安装、配置其它应用程序的烦恼，比较省事
 - 可以享受SuperPuTTY的其它邮电（比如标签页管理，多会话平铺，自行定义会话，会话查找，脚本化等等）
 
**缺点**:
 
 - 只能从session树上发起sftp操作，如果session比较多，要针对 **当前session** 传送文件，得在树上找一阵子
 - 得重新输入密码（不过SuperPuTTY允许你附加命令行参数给PuTTY/KiTTY，比如`-pass "foobar"`）
 - SuperPuTTY不支持自动导入PuTTY/KiTTY的session，而且导入时要先将原来导入的删除（否则会造成重复）－原来我觉得这是它的设计缺陷，但后来放弃了PuTTY/KiTTY的session（只将它们用作模板，这个以后再说）．
 
## 方法三：用zmodem来上传下载文件


**准备工作**: 

  - 首先，你需要用[PuTTY-nd](http://sourceforge.net/projects/putty-nd/files ) 3.0以上版本，因为[原版本的PuTTY不支持zmodem](http://www.chiark.greenend.org.uk/~sgtatham/putty/wishlist/zmodem.html ) ；
  - 其次，你需要在远程主机上安装`lrzsz`包（常用Linux发行版上都收录了这个包）。
 
 **使用方法**: 使用的时候就简单了，
 
   - 要下载文件的时候在Linux服务器上发起`sz somefile.zip`命令就行了（跟其它支持zmodem的客户端不一样的是，PuTTY-nd不会询问保存文件的位置，会直接保存到桌面上）；
   - 要上传的时候，在Linux服务器上敲`rz`命令，PuTTY-nd/KiTTY就会显示打开文件对话框，让你选择要上传的文件。
 
 **优点**:
 
  - 使用起来很方便，不用打开其它程序重新建立ssh连接，也不用一层一层跳转目录；
  - 举一反三，掌握了这个，可在其它支持zmodem的终端上使用，比如Xshell, Mobaxterm, GNu Screen
 
 **缺点**: 
 
   - 要在服务器上安装lrzsz包，如果要管理很多台服务器，正式环境也可能不能随意联网（这样第一次如何安装`lrzsz`也挺费劲），可能会比较麻烦。
   - 一次只能传送单个文件，要传输一个目录的话就得用`tar`或者`zip`打包；
   - 另外，PuTTY-nd的实现不太稳定（有时不工作？）
 
 
> KiTTY 也支持zmodem，不过启用这个功能有点麻烦
>
>  1. 在`%appdata%\KiTTY\kitty.ini`里面设置`[KiTTY]zmodem=yes`
>  2. 下载Windows版本的rz.exe和sz.exe（可从<http://www.9bis.net/kitty/?file=kitty_zmodem.zip>  （被土薔）下载或者从[LePuTTY的二进制包](http://sourceforge.net/projects/leputty/files/ ) 里面提取`win32-lrzsz-0.12.20-bin.zip`）
>  3. 打开KiTTY，在 _Connection/Zmodem_ 里设置`sz.exe`和`rz.exe`的路径，以及下载文件的保存地址
>
> 使用的时候也有点麻烦
>
>  1. 每个session都要设置（除非你先在 _Default Settings_ 里面设置了，然后新建session时会继承）
>  2. 下载时在linux上输入`sz some_file`　后，要在KiTTY窗口菜单上选择 _Zmodem Receive_．文件被存到上面设置的目录（不会让你选目录）
>  3. 上传时要先在linux上输入`rz`，然后再在KiTTY窗口菜单上选择 _Zmodem Upload_
>
> 参考:
>
>  - [KiTTY: ZModem](http://www.9bis.net/kitty/?page=ZModem&amp;zone=en ) (被蔷)
>  - [让KiTTY/Putty支持ZModem](http://ju.outofmemory.cn/entry/143929  )
 
参考: 
 
 - [lrzsz主页](https://ohse.de/uwe/software/lrzsz.html )
 - manpage: [sz](http://manpages.debian.org/cgi-bin/man.cgi?query=sz&apropos=0&sektion=0&manpath=Debian%208%20jessie&format=html&locale=en ) [rz](http://manpages.debian.org/cgi-bin/man.cgi?query=rz&apropos=0&sektion=0&manpath=Debian%208%20jessie&format=html&locale=en )
  

## 方法四：用PuTTY Session Manager + WinSCP/Filezilla
 
如果你觉得zmodem不太方便/稳定，又不想从PuTTY迁移到KiTTY，那还有没有其它办法？好吧，这里还有一个：在[PuTTY Session Manager](http://puttysm.sourceforge.net) 上发起WinSCP或者Filezilla调用。
 
PuTTY Session Manager这个小工具的主要是能够列出所有的putty session（并且你可以将它们组织成树状），你只要双击节点即可调用putty打开相应的会话（它的一个优点是它支持将session按目录组织起来）。

![puttysm tree](http://puttysm.sourceforge.net/images/psm-folders.jpg )
 
PuTTY Session Manager支持对接WinSCP和Filezilla，可以在右键菜单中对选中session发起WinSCP或者Filezilla会话，不再需要输入主机名/端口/用户名:
 
 ![puttysm + winscp](https://www.diigo.com/file/image/braraszdcaqpdbpczcdopaobq/https%3A%2F%2Fsourceforge.net%2Fp%2Fputtysm%2Fbugs%2F_discuss%2Fthread%2Fa1212cf6%2Fb647%2Fattachment%2FOptions-WinScp.png.jpg )
  
 
**优点**：不用自己建立WinSCP的session，同时还可以少输入服务器地址、用户名。
 
**缺点**：

  - 还得自己输入密码，因为PuTTY不会让你保存密码到session里（当然，你可以在putty session或者`pageant`里面配置ssh key自动登录。请参考我上一篇博文: [如何配置ssh免密码登录](http://www.cnblogs.com/bamanzi/p/ssh-login-without-password.html )
  - 树上面节点多了之后，每次要先在这颗树上找到当前putty会话到底是哪一个，这挺费劲


## 方法五: 用MobaXterm自带的sftp面板

[MobaXterm](http://mobaxterm.mobatek.net/ ) 在连接一个ssh会话时会自动在侧边栏显示sftp面板，可以在这里上传下载文件（并且支持与Windows程序的文件拖放）．

![mobaxterm sftp browser](https://www.diigo.com/file/image/braraszdcaqpeqdrzcdopaqcp/Mobaxterm+SFTP+browser.jpg )

**优点**: ssh连接时自动显示sftp面板，并会跟随shell的目录变更（另外，MobaXterm也支持zmodem）

**缺点**:
  - 得从PuTTY迁移到MobaXterm，而MobaXterm虽然有自动导入PuTTY session的功能，但免费版本仅仅支持最多12个会话（规避办法是不用它的session面板，而是每次打开一个本地终端(local terminal)，然后用它里面的ssh命令行来连接远程服务器）
  - sftp面板逐层目录进入比较麻烦（如果你在tmux/screen里面工作）
  - zmodem的支持是今年年初在v8.6新加入的功能，还不太稳定，有时候会出错
            


## 方法五: 用Total Commander + sftp4tc
 
**优点**：复用putty session，不用自己重新建立session，同时还可以少输入服务器地址、用户名
 
**缺点**：
 
 - 还得自己输入密码，或者配置ssh key自动登录
 
 - 不支持kitty
 
 - 逐层目录进入比较麻烦
 
 ### 方法五: MobaXterm
 
 优点: ssh连接时自动显示sftp面板，会跟随目录变更
 
 （另外，它也支持zmodem）
 
 缺点
 
 - 得从PuTTY迁移到MobaXterm，而MobaXterm虽然有自动导入PuTTY session的功能，但免费版本仅仅支持最多12个会话。（规避办法是用它里面的ssh命令行？？）
 
 - sftp面板逐层目录进入比较麻烦（如果你在tmux/screen里面工作） 自动跟随目录变更？
 
 - v7.t?增加的zmodem，它的zmodem功能目前尚不稳定
