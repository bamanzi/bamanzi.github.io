---
layout: post
title: 在安卓上面安装Linux的方式
tags: android, linux
---

## 方法一: 并行安装Linux（不在Android操作系统之上运行，需要设备已经unlocked并且rooted）

我还没玩过。放两个书签:

* [How to Install Ubuntu on Android](http://androlinux.com/android-ubuntu-development/how-to-install-ubuntu-on-android/ )
* [让 Android 手机也具备 Continuum 功能：连接大屏就能当 PC 用](https://linux.cn/article-6983-1.html?utm_source=rss&utm_medium=rss)  ——这个用法比较有价值，但目前似乎还在试验阶段

## 方法二:  容器化（运行在Android操作系统之上，不需要root）

安装[GNURoot](http://apps.store.aptoide.com/app/market/champion.gnuroot/20/10190470/GNURoot )这个安卓应用即可（还需要安装 [GNURoot Debian](http://dtjr0619.store.aptoide.com/app/market/com.gnuroot.debian/29/13892114/GNURoot+Debian ), [GNURoot Wheezy](http://cell1.store.aptoide.com/app/market/champion.gnuroot.wheezy/5/6902139/GNURoot+Wheezy )等rootfs包，然后就可以用`apt-get`安装Debian的armhf包）。采用容器化技术实现，用到了Terminal Emulator for Android、 PRoot和bVNC， 可以跑用户态的应用。我现在在用的是GNURoot Debian，在里面安装了一些命令行工具（mc, ssh, mercurial, vim之类的），方便连到树莓派上面去。我没有安装X应用（从描述上看，应该也是支持的，跑在vncserver里面，然后用VNC查看器连进去操作）。

除了Debian系，也有[GNURoot Gentoo](http://apkx.store.aptoide.com/app/market/champion.gnuroot.gentoo/3/8954739/GNURoot+Gentoo )，[GNURoot Fedora](http://cell1.store.aptoide.com/app/market/champion.gnuroot.fedora/4/6902405/GNURoot+Fedora+Remix )的rootfs。不过[GNURoot Debian](http://dtjr0619.store.aptoide.com/app/market/com.gnuroot.debian/29/13892114/GNURoot+Debian )是最近几个月还在活跃更新的（其它的都有一年没动静了）。

详细说明:
* [在 Android 系统上安装 Debian Linux 与 R （更新 RStudio Server 安装）](https://linuxtoy.org/archives/install-debian-and-r-on-android.html )

![](http://images2015.cnblogs.com/blog/163248/201602/163248-20160213150248277-939506014.png)

![](http://images2015.cnblogs.com/blog/163248/201602/163248-20160213150305981-1197222206.png)

<iframe src="http://m.aptoide.com/embed_apk/dtjr0619/13892114" frameborder="0" width="100%" height="158px"></iframe>
