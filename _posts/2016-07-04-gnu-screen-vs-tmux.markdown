---
layout: post
title: 从Tmux切换到GNU Screen
tags: tmux, gnu-screen, zmodem
---

网上很多地方都说[Tmux](https://tmux.github.io/ )比[GNU Screen](http://www.gnu.org/software/screen )要好用，不过无意间看到这篇[Switching from tmux to GNU Screen](https://xaizek.github.io/2015-11-08/switching-from-tmux-to-gnu-screen/)之后，我发现GNU Screen的窗口/区域概念更好，至少是更适合我（虽然相对Tmux有不少小缺点）。

## 优点1: GNU Screen的窗口/区域/布局概念更适合某些场景

Tmux里面的窗口概念是: 程序是跑在pane里面的，每个window可切分成多个pane，一般我们会并行开多个window．这样每个window多半用于不同的事情．这种方式要把一个window里面的某个pane移动到另外一个windows

GNU Screen的窗口与区域关系更接近Emacs里面buffer与window的关系：

  - gnu screen里面的region相当于tmux里面的pane，而screen的window更类似于跑在tmux pane里面的程序；
  - 与tmux不同的是，一般情况下程序/窗口是隐藏的，每次只把一个程序/窗口切换到当前region来（tmux里面一般情况下所有程序都会在某个window的某个pane里面显示者，除非有其它pane被最大化导致当前pane被隐藏了）

GNU Screen里面没有tmux里面的window那样的东西，它的layout倒是跟tmux的window有点像，虽然我们可以从一个layout切换到另外一个layout，但layout只是region的容器，而不是window的容器，两个layout里面是可以查看同一个应用(window)的．
   
不实际操作一下的话，不一定能感受到上面的差异．网上找到两个图说明两者概念的不同，也许对读者有所帮助（来自[Differences between tmux vs screen - Wesley Tanaka](http://wtanaka.com/node/8136) )

gnu screen:
![gnu screen概念](https://www.diigo.com/file/image/braraszdcapdbqeqzcdooacab/Differences+between+tmux+vs+screen+-+Wesley+Tanaka.jpg )
tmux:
![tmux概念](https://www.diigo.com/file/image/braraszdcapdbqpbzcdooacac/Differences+between+tmux+vs+screen+-+Wesley+Tanaka.jpg )

这样的好处是:

1. 一旦确定了一个layout，剩下的大部分时候都是切换到不同的程序来工作，而不需要切换到不同程序工作时来回调整当前窗格的大小（因为我们很多时候都想让当前程序占据比较大的面积，但又未必是全屏－比如一个窗口看代码，一个窗口调试，还一个窗口查文档）．
2. 两个人（或者两个显示器）可以attach到同一个session，然后各自工作在自己的"视图"上（即可以跟不同的程序交互）－之前在windows上用putty连到开发用机的时候，我总找不到一个好方法让tmux充分利用两个显示器（putty最大化不能占据两个显示器，自己拖又麻烦，就算拖大了，有时从其它单显示器机器连上来tmux的宽度又缩回去了）

不过有几个小地方需要注意:

1. 在当前region下，切换到一个window时，window不会自动根据当前region大小调整窗口(因为它也有可能在另外一个region里面显示)，需要用 `C-a F`调整一下．对应命令是 `fit` (change the window size to the size of the current region).
2. 要attach到一个session的话，需要用命令`screen -r`，但一旦这个session已经被attach了，其它客户端要attach上来，得用`screen -x`
   

## 优点2: zmodem集成

跟远程机器打交道时比较烦人的一个事情是上传下载文件，尤其是要传送文件的源目录或者目标目录在一个较深的路径的时候．zmodem提供一个比较便捷的方法．

至于具体用法，官方的文档[Zmodem - Screen User's Manual](https://www.gnu.org/software/screen/manual/html_node/Zmodem.html )讲得很语焉不详，这篇[GNU Screen and Zmodem | Adam Monsen](http://adammonsen.com/post/256) 写得很详细:

> Send a file from the remote host to the local host:
>
>    1. start a Screen session on the local host
>    2. configure Screen to “catch” zmodem traffic (CTRL-A:zmodem catch<ENTER>)
>    3. execute `sz FILE` from the command line
>    4. hit <ENTER> when Screen brings up the default receive command (`:!!! rz -vv -b -E`)
>    5. bam, the file is available on the local host!
>
> Send a file from the local host to the remote host:
>
>    1. start a Screen session on the local host
>    2. configure Screen to “catch” zmodem traffic (CTRL-A:zmodem catch<ENTER>)
>    3. execute `rz` from the command line (no need to specify filename)
>    4. add local filename when Screen brings up the default send command(`:!!! sz -vv -b`), then hit <ENTER>
>    5. bam, the file is available on the remote host!

从描述上来看，从远端发送一个文件到screen这边还算比较方便，因为大多数情况下我们在远端机器上已经进入了文件所在的目录，只需要直接发起 `sz FILE`　就可以了，screen接收后就存放在它的"当前目录"；不过从screen发送一个文件就有点不爽，因为这里要输入待发送文件的全路径，这里并没有一个浏览文件的功能．

## 其它小问题

### 纵向分割

网上很多比较gnu screen和tmux的文章都列了一个理由是gnu screen不支持纵向分割．不过4.2.0版本(Apr/17/2014)已经支持了( /'-v' parameter to 'split' command for vertical splits/ ).

另外有一点提醒一下: 在漫长的4.1.x时代，一些发行版集成了一个第三方补丁来支持纵向分割，当时实现的纵向分割命令是`vert_split`（而4.2.0版本里面实现的是`split -v`）．如果你的screen不支持`split -v`，那么可以试试有没有`vert_split`这个命令．

### 鼠标

用`mousetrack on`命令即可开始鼠标支持(用`C-a :`输入，或者放入`~/.screenrc`)，开启后可以用鼠标切换region．

但它没有tmux的鼠标能力强，不能`mouse-select-window`的功能（即使你将`hardstatus`配置为显示window列表，也不行)，也不能`mouse-resize-pane`．

在 `~/.screenrc` 里面加入 `termcapinfo screen*|term* ti@:te@` 可以让screen支持用鼠标滚轮来回滚scroll buffer（注意设置此选项需要重启screen session，而即时通过`C-a :`来输入是不行的），但与tmux不同的是它不会自动进入copy-mode,　也就是说需要用`C-a[`进入copy-mode后鼠标滚轮才有作用． 对于上述配置的详细解释请参看[Using the scrollwheel in GNU screen - Stack Overflow](http://stackoverflow.com/questions/359109/using-the-scrollwheel-in-gnu-screen)

### Emacs用户

如果你是emacs用户，要设置以下几个选项:

- `escape ^Bb` 将热键改为C-b, 按`C-b b`的时候才向里面的应用程序发送`C-b`．因为默认的`C-a`在Emacs和命令行里面用的还是比较多的如果这个
- `vbell off` 不想在`C-g`的时候看到什么闪屏，就设置这个吧
- `defflow auto` 不想让`C-s`把屏幕锁住，就需要这个，具体请查看: [Flow Control - Screen User's Manual](http://www.gnu.org/software/screen/manual/html_node/Flow-Control.html#Flow-Control)
- 如果你在Emacs里面用了F12, F9这些功能键的话，不要用ubuntu的byobu（除非你愿意去修改某一方的快捷键）

## 参考资料

### 基本入门

- [GNU Screen (简体中文) - ArchWiki](https://wiki.archlinux.org/index.php/GNU_Screen_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
- [GNU Screen - ArchWiki](https://wiki.archlinux.org/index.php/GNU_Screen)
- [对话 UNIX: 使用 Screen 创建并管理多个 shell](http://www.ibm.com/developerworks/cn/aix/library/au-gnu_screen/)
- [linux 技巧：使用 screen 管理你的远程会话](https://www.ibm.com/developerworks/cn/linux/l-cn-screen/)
- [Top 10 Awesome Linux Screen Tricks – UrFix's Blog](https://blog.urfix.com/top-10-awesome-linux-screen-tricks/)

### gnu screen 与 tmux 比较

- [Why You Should Try tmux Instead of screen](http://dominik.honnef.co/posts/2010/10/why_you_should_try_tmux_instead_of_screen/)
- [Switching from tmux to GNU Screen](https://xaizek.github.io/2015-11-08/switching-from-tmux-to-gnu-screen/)
- [Differences between tmux vs screen - Wesley Tanaka](http://wtanaka.com/node/8136)

### 速查卡

- [dayid's tmux & screen cheat-sheet](http://www.dayid.org/comp/tm.html) 非常值得推荐的速查卡
- [screen Quick Reference](http://aperiodic.net/screen/quick_reference )
