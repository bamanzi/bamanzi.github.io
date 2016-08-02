---
layout: post
title:  WindowMaker 一个轻量级桌面
tags: x11, cygwin, raspi
---

最近工作上需要在远程Linux上运行一个桌面（我需要跑Netbeans, Firefox, Emacs和Seafile)，但是MobaXterm自带的两个窗口管理器twm/fvwm 都过于简陋了（而且我觉得都比较无趣）; 另一方面一直觉得树莓派Raspbian自带的桌面也不好用，我想找个替代品，于是我又想起了以前玩过一阵的WindowMaker。

WindowMaker（常简称wmaker，因为它的主程序文件是这个名字）历史也很久，所以有些设计思想跟现在流行的window manager或者desktop environment差异比较大（从另一个角度说，现在流行的window manager都太雷同了，也就平铺式的有点新意），但正因为如此，它也可以给我们增加一点新奇感。

WindowMaker的设计是基于NeXTSTEP的，另外还有一个叫做GNUStep的项目也是试图克隆NeXTSTEP的（甚至编程语言也是Objective-C），它的目标定位是整个desktop environment（包含了文件管理器在、图片查看器、界面设计器等），并且选择了WindowMaker作为它的window manager（不过这个是用C语言开发的）。不过我不太喜欢GNUstep里面其它的组件，用起来都慢吞吞而且不稳定。

WindowMaker的几个优点:

- 绝大部分设置（全局快捷键也可以！）可以通过自带的WPrefs程序即可设置，不必自己编辑配置文件，并且大多数是即时生效的;
- 整体功能比较紧凑，不会需要很多额外的扩展包（是的，听说fvwm是有很强的可配置性，可我难得折腾）
- Cygwin官方仓库有这个程序（在我这个场景里是挺重要的一点）
- 而且，整体小巧、快捷，占用的内存少，所以很适合我的这两个场景（Cygwin和Raspbian）

当然，缺点也挺明显

- appicon/miniwindows/dock/clip跟现在大家习惯的“任务栏”差别比较大，可能得适应一阵
- 小部件不是太丰富

![NeXTSTEP-like window manager for X](https://screenshots.debian.net/screenshots/000/000/129/large.png )

## dock/clip是任务栏吗

首先说一下wmaker里面几个相关的概念: 

- **appicon**: 每个程序跑起来时，会在桌面底部显示一个图标，这个图标称为**application icon**，常简称**appicon**。程序关掉之后，对应的图标就消失了。我们可以拖动appicon到其它位置（比如随意移动，或者移动到**dock**或者**clip**上）
- **dock**: 桌面最右边（缺省配置在右边）有一列图标，这列图标就叫做**dock**。appicon拖动到这一列时就会“停靠”在这里了，即使程序关掉也不会消失，并且重启WindowMaker它也还在，可以直接点击它来启动这个应用程序（当然，可以定制启动方法，还可以定制一个鼠标中键点击时执行的命令）——简单地说，dock比较类似于Windows 7里面固定在任务栏的程序 (详细说明： [Window Maker - Dock](http://windowmaker.org/guidedtour/dock.html) )
- **clip**: 桌面左上角还有一个回型针一样的图标，它的名字是**clip**，但跟“剪贴”没有什么关系，其实是工作区(workspace)切换器（点击两个小三角会切换到上一个/下一个工作区）。不过它还有一个作用：appicon可以停靠到它旁边，这样这个appicon对应的应用就只跟当前workspace绑定（当桌面切换到其它工作区时，这个应用的窗口就不会显示了） 关于clip的详细说明： [Window Maker - Clip](http://windowmaker.org/guidedtour/clip.html)
- **miniwindow**: 除了appicon之外，每个窗口最小化之后还有另外一个图标，这个被称为**miniwindow**。

图

图

这里面有些小问题：

> 但是多用一阵发现我不太习惯它的dock/clip设计，一方面它可以启动应用，另一方面它又有任务栏的作用（一个appicon停靠到dock后，原来的appicon就不显示了，双击它可以激活应用的主窗口），但只能针对已经dock/clip的应用，其它应用不在这里，而是在下面(appicon)。而且每个应用有两个图标，一个是appicon，另一个是最小化的图标，一般情况下双击dock图标可以激活应用程序窗口，但不能激活已经最小化的窗口。
> 基于这个问题，如果你要切换回某个程序的话，有时要点右边的dock，有时需要点击左上角的clip，有时要点击下方的appicon，很分裂。感觉要解决这个问题，得把dock栏和appicon栏放在同一排，但*dock只能是竖着的* (appicon的位置和方向倒是可以在wprefs里面修改），同时将dock和appicon都放在右侧竖着的话，多开几个程序就没空间了。
> 网上也有人问能不能将dock搞成横着的，一个答复是变通地用0.95.5(Aug/2013)里面新增的抽屉（drawer）功能，但我试了一下发现，dock里面缺省那个按钮不能是drawer，也不能删除，得另外添加一个drawer按钮，这样至少需要两个按钮 :-( ，放在右下角的话也只能算是可以凑合着用。
>
> 另一个思路是只将dock作为launcher来用，不抢任务栏的工作（即dock之后仍然在桌面下方显示appicon，可以通过点击appicon来激活窗口），但好像没有看见开关。文档里有这个功能的描述，没有说如何关闭 http://windowmaker.org/guidedtour/dock.html
> 
> 这里另外提供了一个方法 http://www.linuxquestions.org/questions/debian-26/windowmaker-icons-206322-print/  : 禁用dock，只用clip，而且clip还有attract icons的功能，更像任务栏了。这种这个方法当然也有副作用：clip吸附的程序只在当前workspace可见

## dockapps

这是使用wmaker需要掌握的第二个概念。wmaker是用dock来替代其它窗口管理器里面的板（panel）的，如果你有往panel上嵌入一些小部件的需求时（比如想显示一下日期/时间/天气，或者显示一下CPU利用率曲线），wmake里面就是用 **dockapps** 来解决。

dockapps其实只是一些小程序，不过程序窗口都跟wmaker的dock图标一样大小，这样的应用程序也没有appicon，可以拖到dock或者clip上（不过它也可以放在其它位置独立显示）。

- wmaker官网上的dockapps列表：http://windowmaker.org/dockapps/
- Debian WindowMaker Team 维护的应用列表（里面里面除了wmaker其它大部分都是dockapps）https://qa.debian.org/developer.php?login=pkg-wmaker-devel%40lists.alioth.debian.org

## 通知栏（system tray / notification area）

现在有很多程序（比如网络管理器、Dropbox、剪贴板监视器Glipper/Parcellite等）会在启动时隐藏主窗口，只在通知栏(system tray / notification area)显示一个图标。所以我们需要以dockapps形式存在的通知栏容器，将那些应用的通知栏图标收纳在里面。

虽然archwiki上推荐的是[stalonetray](http://stalonetray.sf.net ) ，但我实际试验了一下还是[wmsystemtray](http://wmsystemtray.sf.net ) 跟wmaker配合得更好一些，而且也更稳定一些。图标个数增加后，stalonetray大小扩展比较奇怪，这也许跟她的设计目标并不是针对 wmaker 有关; 而wmsystemtray是只能一次显示4个图标，通过一个切换按钮来切换到下一组4个图标。

图

## 自动启动程序

对于放到dock/clip的程序图标，直接在图标属性里面勾选 /Start when Window Maker is started/ 就可以让该应用跟随wmaker自动启动了。

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

## window snapping功能

之前我在Linux上一直用gnome 2/mate desktop比较多，它支持window snapping，不过比较简单，只支持左右半屏。

wmaker也有类似的能力，不仅可以最大化半屏，而且还可以只占据四角1/4，但需要0.95.5 (2013 Aug)以上的版本。而且在0.95.5里面只能在WPrefs里添加快捷键来操作（Debian 8 jessie和Ubuntu 14.04目前是这个版本），而去年月发布的0.95.7版本增加了直接通过鼠标拖放和窗口菜单来完成（注意官网的[News](http://windowmaker.org/news.php )页面只更新到0.95.5版本，后面的变化需要直接看代码库的[News文件](http://repo.or.cz/wmaker-crm.git/blob/wmaker-0.95.7:/NEWS )），如果你想要这个功能的话，Ubuntu可以用这个ppa https://launchpad.net/~profzoom/+archive/ubuntu/wmaker , Debian得自己编译了。


## gtk主题

如果你想让你的gtk/gnome程序跟wmaker桌面的风格更贴近一点的话，可以安装 [OneStepBack 主题](http://gnome-look.org/content/show.php/OneStepBack?content=170904 )



