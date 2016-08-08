---
layout: post
title:  轻量级桌面WindowMaker上手指南
tags: x11, cygwin, raspi
---

最近工作上需要在远程Linux上运行一个桌面（我需要跑Netbeans, Firefox, Emacs和Seafile)，但是MobaXterm自带的两个窗口管理器twm/dwm 都过于简陋了（而且我觉得都比较无趣）; 另一方面一直觉得树莓派Raspbian自带的桌面也不好用，我想找个替代品，于是我又想起了以前玩过一阵的WindowMaker。

WindowMaker（常简称wmaker，因为它的主程序文件是这个名字）历史也很久，所以有些设计思想跟现在流行的window manager或者desktop environment差异比较大（从另一个角度说，现在流行的window manager都太雷同了，也就平铺式的有点新意），但正因为如此，它也可以给我们增加一点新奇感。

WindowMaker的设计是基于NeXTSTEP的，另外还有一个叫做GNUStep的项目也是试图克隆NeXTSTEP的（甚至编程语言也是Objective-C），它的目标定位是整个desktop environment（包含了文件管理器在、图片查看器、界面设计器等），并且选择了WindowMaker作为它的window manager（不过这个是用C语言开发的）。不过我不太喜欢GNUstep里面其它的组件，用起来都慢吞吞而且不稳定。

![NeXTSTEP-like window manager for X](https://screenshots.debian.net/screenshots/000/000/129/large.png )

WindowMaker的几个优点:

- 绝大部分设置（全局快捷键也可以！）可以通过自带的WPrefs程序即可设置，不必自己编辑配置文件，并且大多数是即时生效的;
- 整体功能比较紧凑，不会需要很多额外的扩展包（是的，听说fvwm是有很强的可配置性，可我难得折腾）
- Cygwin官方仓库有这个程序（在我这个场景里是挺重要的一点）
- 而且，整体小巧、快捷，占用的内存少，依赖也比较少，所以很适合我的这两个场景（Cygwin和Raspbian）

当然，缺点也挺明显

- appicon/miniwindows/dock/clip跟现在大家习惯的“任务栏”差别比较大，可能得适应一阵
- 小部件不是太丰富（主要原因还的确在于wmaker比较古老，现在的人对它兴趣不太大了）

参考: 

- [Window Maker - ArchWiki](https://wiki.archlinux.org/index.php/Window_Maker )
- [In Depth: 5 of the best lightweight window managers for Linux](http://technetyes.blogspot.com/2011/07/techradar_03.html ) （被薔，我放了一份副本在[这里](https://bamanzi.github.io/scrapbook2016/data/20160718063037/index.html ) ）
- [Why I use Window Maker - UNIX Administratosphere](https://administratosphere.wordpress.com/2011/07/05/why-i-use-window-maker/ )


## dock/clip是任务栏吗

首先说一下wmaker里面几个相关的概念: 

- **appicon**: 每个程序跑起来时，会在桌面底部显示一个图标，这个图标称为**application icon**，常简称**appicon**。程序关掉之后，对应的图标就消失了。我们可以拖动appicon到其它位置（比如随意移动，或者移动到**dock**或者**clip**上）
- **dock**: 桌面最右边（缺省配置在右边）有一列图标，这列图标就叫做**dock**。appicon拖动到这一列时就会“停靠”在这里了，即使程序关掉也不会消失，并且重启WindowMaker它也还在，可以直接点击它来启动这个应用程序（当然，可以定制启动方法，还可以定制一个鼠标中键点击时执行的命令）——简单地说，dock比较类似于Windows 7里面固定在任务栏的程序 (详细说明： [Window Maker - Dock](http://windowmaker.org/guidedtour/dock.html) )
- **clip**: 桌面左上角还有一个回型针一样的图标，它的名字是**clip**，但跟“剪贴”没有什么关系，其实是工作区(workspace)切换器（点击两个小三角会切换到上一个/下一个工作区）。不过它还有一个作用：appicon可以停靠到它旁边，这样这个appicon对应的应用就只跟当前workspace绑定（当桌面切换到其它工作区时，这个应用的窗口就不会显示了） 关于clip的详细说明： [Window Maker - Clip](http://windowmaker.org/guidedtour/clip.html)
- **miniwindow**: 除了appicon之外，每个窗口最小化之后还有另外一个图标，这个被称为**miniwindow**。


~~但这里面有些小问题：~~

> ~~但是多用一阵发现我不太习惯它的dock/clip设计，一方面它可以启动应用，另一方面它又有任务栏的作用（一个appicon停靠到dock后，原来的appicon就不显示了，双击它可以激活应用的主窗口），但只能针对已经dock/clip的应用，其它应用不在这里，而是在下面(appicon)。而且每个应用有两个图标，一个是appicon，另一个是最小化的图标，一般情况下双击dock图标可以激活应用程序窗口，但不能激活已经最小化的窗口。~~
>
> ~~基于这个问题，如果你要切换回某个程序的话，有时要点右边的dock，有时需要点击左上角的clip，有时要点击下方的appicon，很分裂。感觉要解决这个问题，得把dock栏和appicon栏放在同一排，但*dock只能是竖着的* (appicon的位置和方向倒是可以在wprefs里面修改），同时将dock和appicon都放在右侧竖着的话，多开几个程序就没空间了。~~
>
> ~~网上也有人问能不能将dock搞成横着的，一个答复是变通地用0.95.5(Aug/2013)里面新增的抽屉（drawer）功能，但我试了一下发现，dock里面缺省那个按钮不能是drawer，也不能删除，得另外添加一个drawer按钮，这样至少需要两个按钮 :-( ，放在右下角的话也只能算是可以凑合着用。~~
>
> ~~另一个思路是只将dock作为launcher来用，不抢任务栏的工作（即dock之后仍然在桌面下方显示appicon，可以通过点击appicon来激活窗口），但好像没有看见开关。文档里有这个功能的描述，没有说如何关闭(http://windowmaker.org/guidedtour/dock.html )~~
> 
> ~~这里另外提供了一个方法 http://www.linuxquestions.org/questions/debian-26/windowmaker-icons-206322-print/  : 禁用dock，只用clip，而且clip还有attract icons的功能，更像任务栏了。这种这个方法当然也有副作用：clip吸附的程序只在当前workspace可见~~

> 2016 Aug.08更新: 
>
> 这两天发现一个更好的解决办法，不用纠结于上面这些烦人的问题了： 直接跑一个其它桌面(desktop environment)里面的面板就可以了——当然，是要那种能够支持任务列表(task list)的面板，这样wmaker的dock就只是用作大家通常意义上理解的dock，也就是用于启动常用应用和收纳一些dockapps
>
> 当然，我们最好是挑一个依赖比较少的面板。树莓派Raspbian里面默认就有的 [lxpanel](http://wiki.lxde.org/en/LXPanel ) 是一个不错的选择，而且它还有“通知栏”插件，下面说到的 wmsystemtray/ 也可以不用装了。
>
> 这里有一个WindowMaker + LXPanel的效果图: https://www.vivaolinux.com.br/screenshot/Window-Maker-WMaker-green-Lxpanel-no-Linux-Mint-17/

## dockapps

这是使用wmaker需要掌握的第二个概念。wmaker是用dock来替代其它窗口管理器里面的板（panel）的，如果你有往panel上嵌入一些小部件的需求时（比如想显示一下日期/时间/天气，或者显示一下CPU利用率曲线），wmake里面就是用 **dockapps** 来解决。

dockapps其实只是一些小程序，不过程序窗口都跟wmaker的dock图标一样大小，这样的应用程序也没有appicon，可以拖到dock或者clip上（不过它也可以放在其它位置独立显示）。

- wmaker官网上的dockapps列表：http://windowmaker.org/dockapps/
- Debian WindowMaker Team 维护的应用列表（里面里面除了wmaker其它大部分都是dockapps）https://qa.debian.org/developer.php?login=pkg-wmaker-devel%40lists.alioth.debian.org
- openbox的网页上有一个比较全的dockapps一览表（每个都有功能说明和截图） http://lxlinux.com/dock.html

## 自动启动程序

对于放到dock/clip的程序图标，直接在图标属性里面勾选 *Start when Window Maker is started* 就可以让该应用跟随wmaker自动启动了。

![dock](http://windowmaker.org/guidedtour/images/docks.jpg)

对于不适合拖到dock/clip的（比如那些启动后不显示窗口只有通知栏图标的程序： nm-applet, seafile-applet等），可以编辑文件 `~/GNUstep/Library/WindowMaker/autostart` （它是个脚本，按shell脚本的写法来添加你要启动的程序就就可以了，记得启动GUI程序时在命令后面添加 `&` 号）

``` Shell
#!/bin/bash
xset m 20/10 4

wmsystemtray &
.nutstore/dist/bin/nutstore-pydaemon.py &
nm-applet &
glipper &
wmcalclock &
```

## 通知栏（system tray / notification area）

现在有很多程序（比如网络管理器、Dropbox、剪贴板监视器Glipper/Parcellite等）会在启动时隐藏主窗口，只在通知栏(system tray / notification area)显示一个图标。所以我们需要以dockapps形式存在的通知栏容器，将那些应用的通知栏图标收纳在里面。

虽然archwiki上推荐的是[stalonetray](http://stalonetray.sf.net ) ，但我实际试验了一下还是[wmsystemtray](http://wmsystemtray.sf.net ) 跟wmaker配合得更好一些，而且也更稳定一些。图标个数增加后，stalonetray大小扩展比较奇怪，这也许跟她的设计目标并不是针对 wmaker 有关; 而wmsystemtray是只能一次显示4个图标，通过切换按钮来切换到下一组／上一组4个图标（如果以 `wmsystemtray --small`方式启动，它会采用更小的图标，这样一次可以显示９个图标）．

使用方法:

1. 安装`wmsystemtray`包: `apt install wmsystemtray`
2. 在上一节所说的 `autostart` 文件中添加一行 `wmsystemtray &` （注意结尾的 `&`），这一行放在前面，确保那些需要显示通知栏图标的程序在之后启动．

![wmsystemtray 4 icons](https://bamanzi.github.io/scrapbook2016/data/20160805173353/icon.png )
![wmsystemtray 9 icons](https://bamanzi.github.io/scrapbook2016/data/20160805173353/screenshot2_001.png )
(上两图来自: [wmsystemtray - dockapps.windowmaker.org](http://kfo.ath.cx/windowmaker/2012_windowmaker.info/file.php/id/355 )


## window snapping功能

之前我在Linux上一直用gnome 2/mate desktop比较多，它支持window snapping，不过比较简单，只支持左右半屏。

wmaker也有类似的能力，不仅可以最大化半屏，而且还可以只占据四角1/4面积，但需要0.95.5 (2013 Aug)以上的版本。而且在0.95.5里面只能在WPrefs里添加快捷键来操作（Debian 8 jessie和Ubuntu 14.04目前是这个版本），而去年月发布的0.95.7版本增加了直接通过鼠标拖放和窗口菜单来完成（注意官网的[News](http://windowmaker.org/news.php )页面只更新到0.95.5版本，后面的变化需要直接看代码库的[News文件](http://repo.or.cz/wmaker-crm.git/blob/wmaker-0.95.7:/NEWS )），如果你想要这个功能的话，Ubuntu可以用这个ppa https://launchpad.net/~profzoom/+archive/ubuntu/wmaker , Debian得自己编译了。

![wmaker window snapping](http://images2015.cnblogs.com/blog/163248/201608/163248-20160806214214418-154154725.png )

## 全局快捷键

对于WindowMaker自身的功能（比如打开桌面菜单，打开窗口菜单，最大化窗口，切换工作区等），可以在它的偏好设置程序 *WPrefs* 中设置快捷键（这个程序可以通过桌面菜单 *WindowMaker->Preferences*打开，也可以通过命令行`WPrefs`打开．　打开后切换到倒数第五个图标 *Keyboard Shortcut Preferences*即可）

![configuring shortcuts in WPrefs](https://bamanzi.github.io/scrapbook2016/data/20160717165100/s1600-keyboardshortcutsprefs.png )

如果要设置全局快捷键来启动一个程序（或者执行一段脚本），也是在 *WPrefs* 里面设置，但是在倒数第六个图标 *Applications Menu Definition*　里面．我们得拖一个 *Run Program* 按钮到菜单上，设置对应的快捷键和要运行的命令．

![configuring menu in WPrefs](https://bamanzi.github.io/scrapbook2016/data/20160717165100/s1600-applicationsmenudefs.png )

参考:

- [Get Productive in Window Maker: Keyboard Shortcuts for Fun and Profit - Window Maker and I: busprof's Blog](http://windowmakerandi.blogspot.com/2013/02/get-productive-in-window-maker-keyboard.html ) （但被薔。我截取了一份副本在[这里](https://bamanzi.github.io/scrapbook2016/data/20160717165100/index.html )）

> 另外有一个小细节要注意: 在默认的X11配置下，NumLock键也是一个修饰键(modifier key)，所以WPrefs设置快捷键时会将这个键考虑在内．比如本来想设置`win+r`这个快捷键，但NumLock打开时，WPrefs会捕捉为 `Mod2+Mod4+r` (我这里NumLock映射为`Mod2`, win键被映射为`Mod4`．用`xmodmap`可以查到这个信息）．然后当你使用时，如果NumLock关闭了，只按 `win+r`　是不能启动这个程序的．
>
> 详细说明和解决方法可以参考: [Num Lock Can Bork Your Keyboard Shortcuts in Window Maker - Window Maker and I: busprof's Blog](http://windowmakerandi.blogspot.com/2013/02/num-lock-can-bork-your-keyboard.html ) （但被薔，我截取了一份副本在[这里](https://bamanzi.github.io/scrapbook2016/data/20160717172455/index.html )）


## gtk主题

如果你想让你的gtk/gnome程序跟wmaker桌面的风格更贴近一点的话，可以安装 [OneStepBack 主题](http://gnome-look.org/content/show.php/OneStepBack?content=170904 )


 

