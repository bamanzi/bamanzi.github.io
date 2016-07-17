---
layout: post
title: 彩色的Shell
tags: shell, bash, zsh
---

我常在命令行下工作，以前老被同事说＂你整天在那个黑窗口上倒腾什么？＂　现在这个问题变成了＂你这个花花绿绿的窗口是什么东西?＂

其实都是同一个东西：一个兼容于xterm的终端窗口，要么是PuTTY/KiTTY，要么是GNOME Terminal/Xfce Terminal/LXTerminal．不过我这半年将很多东西配上了颜色．

倒不是说我想搞得很花哨，而是有彩色的文字夹杂其中的话，文字的辨识度要高一些－－你是不是有很多时候在一大段输出当中找寻提示符上一次出现的位置? 是不是在里面找有没有某个单词？

### 1. Shell本身

#### 1.1 提示符

##### bash

通过修改环境变量 `PS1 `实现，只要里面包含适当的[ANSI color sequence](http://stackoverflow.com/questions/15682537/ansi-color-specific-rgb-sequence-bash/15712766)即可实现彩色．Debian/Ubuntu上bash提示符号默认有一份彩色配置，但要求自己编辑  `~/.bashrc`，设置 `force_color_prompt=yes`　即可（找到  `force_color_prompt=yes` 这一行并删除前面的注释符号）．

	# uncomment for a colored prompt, if the terminal has the capability; turned
	# off by default to not distract the user: the focus in a terminal window
	# should be on the output of commands, not on the prompt
	force_color_prompt=yes

CentOS默认没有彩色配置，可以用这两个在线工具来配置： [.bashrc generator: create your .bashrc PS1 with a drag and drop interface](http://bashrcgenerator.com/)　和 [Bash $PS1 Generator 2.0](https://www.kirsle.net/wizards/ps1.html)　（后面这个好像被蔷了）．不过我在一些不想＂深度定制＂的机器上都是用下面的zsh方法来快速搞定．

##### zsh

没仔细研究怎么配置，好像是[通过修改环境变量PROMPT实现](http://zsh.sourceforge.net/Intro/intro_14.html)．不过用zsh的人一般都会用[oh-my-zsh]()或者[prezto](https://github.com/sorin-ionescu/prezto)套件，里面都提供了丰富的配置模板．

不过zsh自带的也不少（效果参见 [Zsh themes for prompts screenshots | blog post](http://bneijt.nl/blog/post/zsh-themes-for-prompts-screenshots/) ），我在不特别常用的机器上一般都是如下几行：

	autoload -Uz promptinit
	promptinit
	prompt adam2
	# 用 prompt -l 可以列出所有的提示符配置，有兴趣可以逐个体验

我比较喜欢adam2这个，因为它可以醒目地将一次次的输入输出分隔开来．


![](https://bytebucket.org/bamanzi/notes-zim/raw/default/blog/2016/04/colorful-shell/adam2_on_black.png)

图片来自: [Zsh themes for prompts screenshots | blog post](http://bneijt.nl/blog/post/zsh-themes-for-prompts-screenshots/ )


#### 1.2 命令行语法高亮

曾经试过用 [fish](http://fishshell.com/) 作为日常shell，但最后因为跟bash差异太大而放弃了（太多东西不兼容了，而zsh在兼容bash这方面就非常好），不过fish彩色的命令行给我留下了很深的印象：

![](https://bytebucket.org/bamanzi/notes-zim/raw/default/blog/2016/04/colorful-shell/fish-colors.png)

但bash上面是搞不了这个功能了，zsh倒是有一个[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting )，安装方法如下: 

	mkdir -p ~/.zsh/plugins
	git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.zsh/plugins/zsh-syntax-highlighting
	echo "source ~/.zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ~/.zshrc
	source ~/.zshrc

![](https://bytebucket.org/bamanzi/notes-zim/raw/default/blog/2016/04/colorful-shell/zsh-syntax-higlighting-preview.png)

### 2. ls

#### 2.1 ls --color

大多数情况下，你执行  `ls --color ` 就可以了，它会用彩色来区分不同的文件类型．

![](https://bytebucket.org/bamanzi/notes-zim/raw/default/blog/2016/04/colorful-shell/LS_output.jpg )

(此图来自: [More than just colors: My bash settings | blogginess](http://www.joelbrock.org/blog/2009/09/more-than-just-colors-my-bash-settings/) )

你可以添加  `alias ls='ls --color=auto'  ` 到你的  `~/.bashrc `.  `auto ` 这个值可以确保你将  `ls ` 的输出重定向到一个文件时不会产生问题．

如果你想要详细了解这个功能，可以阅读 coreutils　文档里面的相应章节： [GNU Coreutils: General output formatting](http://www.gnu.org/software/coreutils/manual/html_node/General-output-formatting.html#General-output-formatting), [GNU Coreutils: dircolors invocation](http://www.gnu.org/software/coreutils/manual/html_node/dircolors-invocation.html#dircolors-invocation) (注意配置文件的详细说明其实在  `dircolors --print-database ` 这个命令的输出里面）．这里还有一个在线配置工具: [LSCOLORS Generator](http://geoff.greer.fm/lscolors/) －－不过我从来没去配过细节．

> 2016/06/02 补充:　默认配置下主要文件类型的对应颜色如下（另外默认配置里面还有不少按扩展名配置的颜色，这里不详细说明了）
> ![](http://www.cyberciti.biz/faq/wp-content/uploads/2008/05/linux-file-permissions-ls-color1.png )

#### 2.2 k

 `ls --color ` 基本上是按照文件类型来确定颜色的．如果你想按照文件大小，新旧乃至git 状态来显示不同颜色，那么可以用这个针对zsh的小脚本: <https://github.com/supercrabtree/k>

	wget -c https://github.com/supercrabtree/k/archive/master.zip -O k.zip
	unzip k.zip
	mkdir ~/.zsh/plugins
	cp k-master/k.sh ~/.zsh/plugins
	source ~/.zsh/plugins/k.sh

然后执行  `k ` 就可以了（不过跟  `ls --color `　不同的是，颜色不是显示在文件名上的，而是大小／时间等列．

![](https://bytebucket.org/bamanzi/notes-zim/raw/default/blog/2016/04/colorful-shell/zsh-k-file-size-colors.jpg)

> 2016/06/02 补充:　看到两个ls的彩色版
>
> `exa` <https://github.com/ogham/exa>
> ![](https://github.com/ogham/exa/raw/master/screenshots.png )
> `ls++` <https://github.com/trapd00r/ls-->
> ![](http://teom.org/wp-content/uploads/2012/05/shell5.png?w=600 )

### 3. 彩色的文档阅读

#### 3.1 彩色的manpage阅读器:

第一个办法如下，不用安装任何其它东西（前提是采用 `less `作为pager -- 虽然一般情况下默认都是用这个）:

```sh
	export LESS_TERMCAP_mb=$(tput bold; tput setaf 2) # green
	export LESS_TERMCAP_md=$(tput bold; tput setaf 6) # cyan
	export LESS_TERMCAP_me=$(tput sgr0)
	export LESS_TERMCAP_so=$(tput bold; tput setaf 3; tput setab 4) # yellow on blue
	export LESS_TERMCAP_se=$(tput rmso; tput sgr0)
	export LESS_TERMCAP_us=$(tput smul; tput bold; tput setaf 7) # white
	export LESS_TERMCAP_ue=$(tput rmul; tput sgr0)
	export LESS_TERMCAP_mr=$(tput rev)
	export LESS_TERMCAP_mh=$(tput dim)
	export LESS_TERMCAP_ZN=$(tput ssubm)
	export LESS_TERMCAP_ZV=$(tput rsubm)
	export LESS_TERMCAP_ZO=$(tput ssupm)
	export LESS_TERMCAP_ZW=$(tput rsupm)
```

然后用  `man date `　就可以看到彩色的manpage了．

如果觉得这个麻烦，不太容易记得住的话，可以用另一个方法：安装 [most](http://www.jedsoft.org/most/) ，然后执行  `PAGER=most man date ` 就能看到彩色的mapage了（你也可以将  `export MANPAGER=most ` 添加到  `~/.bashrc `，这样以后  `man date ` 就能直接调用这个  `most ` 来阅读 manpage）．

![](https://bytebucket.org/bamanzi/notes-zim/raw/default/blog/2016/04/colorful-shell/colour-man-pages.png)

出处： [Unix / Linux: Display Color Man Pages](http://www.cyberciti.biz/faq/unix-linux-color-man-pages-configuration/)

#### 3.2 彩色的texinfo阅读器

安装 pinfo 就可以了．

![](https://bytebucket.org/bamanzi/notes-zim/raw/default/blog/2016/04/colorful-shell/pinfo.png)

参考： [pinfo Linux / Unix Command: Read Info Documentation in Colors](http://www.cyberciti.biz/open-source/command-line-hacks/linux-command-pinfo-for-colorful-info-pages/)

### 4. git与mercurial的彩色配置

#### 4.1 git

比较新一点的git　 (>= 1.8.4，因为[该版本中将 ''color.ui'' 的默认值改为了 ''auto''](https://github.com/git/git/blob/master/Documentation/RelNotes/1.8.4.txt#L178) ）都会自动在  `git status `,  `git log `,  `git diff ` 等等地方采用颜色．

如果你的git比较老，可以用下面的方法来开启颜色支持：

	git config --global color.ui auto

当然也可以详细设置各个子命令是否要启用颜色支持:

```sh
	git config --global color.status always
	git config --global color.grep auto
	git config --global color.diff auto
	git config --global color.branch never
	git config --global color.interactive auto
```sh

详细的说明请参考 [git -config 的手册](https://git-scm.com/docs/git-config#_variables)（或者运行  `git config --help `）

参考:
- [Git显示漂亮日志的小技巧](http://www.coolshell.cn/articles/7755.html)
- [让Git的输出更友好: 多种颜色和自定义log格式](https://www.pureweber.com/article/git-pretty-output/%20)

#### 4.2 mercurial

启用 [Color 扩展](http://mercurial.selenic.com/wiki/ColorExtension) 可以给  `hg status ` ,   `hg log `和  `hg diff ` 添加颜色．

**配置方法**: 编辑  `~/.hgrc `

	[extensions]
	color =

**效果**:  

![](https://bytebucket.org/bamanzi/notes-zim/raw/default/blog/2016/04/colorful-shell/hg-diff-color.png)

具体颜色还可以自行定义，详情请参考 [ColorExtension　的说明](https://www.mercurial-scm.org/wiki/ColorExtension)．


参考: [mercurial的几个易用性小技巧 - 巴蛮子 - 博客园](http://www.cnblogs.com/bamanzi/p/mercurial-a-few-tips.html)


### 5 高亮输出中的指定文字

有时候我们需要在一个程序的输出中找到某个单词，  `grep --color ` 是比较常用的一个方法：

 `grep --color ` 虽然挺不错（尤其是不需要另行安装），但它是一个 **过滤** 工具．然后有些时候我们只是要 **高亮** 输出中的某些文字，而 **不想过滤** 掉其它行，但很奇怪这样的诉求没有在基本工具集里面提供。

#### 5.1 hhighlighter (bash插件)

<<https://github.com/paoloantinori/hhighlighter>>

这是一个bash脚本，它提供了一个h()函数（shell函数也是一种命令），可以用来高亮

安装:

```sh
	  apt-get install ack-grep
	  # or: curl http://beyondgrep.com/ack-2.14-single-file > ~/bin/ack && chmod 0755 ~/bin/ack
	  mkdir -p ~/.bash/plugins/
	  wget 'https://github.com/paoloantinori/hhighlighter/raw/master/h.sh' -O ~/.bash/plugins/h.sh
	  echo "source ~/.bash/plugins/h.sh"
	  source ~/.bash/plugins/h.sh
```

使用:

	  $ h
	  usage: YOUR_COMMAND | h [-i] [-d] pattern1 pattern2...
	  	-i : ignore case
	  	-d : disable regexp
	  	-n : invert colors

说明: hhighlighter传递多个参数时，每个参数会自动采用不同的颜色来高亮

#### 5.2 colorex

<<https://github.com/Scopart/colorex/>>

这个小工具跟 hhighlighter 功能差不多，区别主要在实现层面： colorex是采用python实现的，而且是个独立脚本，比较容易在其它shell下面用．另外，它也不需要其它工具（比如hhighlighter需要ack）

安装:

	  wget 'https://github.com/Scopart/colorex/raw/master/colorex' -O ~/bin/colorex
	  chmod a+x ~/bin/colorex

使用:

```sh
	  # to display every word "ERROR" in red of foo.txt
	  colorex --red ERROR foo.txt
	
	  # to watch logfile.txt displaying "WARNING" in yellow and "INFO" in green
	  tail -f logfile.txt | colorex -y WARNING --green INFO
	
	  # to display "(" and ")" contained in foo.txt in blue
	  colorex -b '\(|\)' foo.txt
```

略微有点不爽的是，必须指定颜色参数，多个关键字时得指定多个颜色参数（不要被  `colorex --help ` 显示的 `-N ` 参数误导了，虽然它说是 " `display with random colors `　"，但并不是我们通常理解的随机选用一个颜色 :-(）

参考:  <https://inconsolation.wordpress.com/2014/08/03/colorex-i-might-as-well-include-them-all/>


### 6. 支持语法高亮的编辑器 



#### 6.1 vim

首先确认你的vim是支持语法高亮的．确认方法是执行  `:version ` 查看里面 `syntax ` 一项是  `+syntax ` 还是  `-syntax `，如果是后者就说明语法支持没有编译进来（另一种方法是执行  `:syn ` ，如果得到错误信息  `E319: Sorry the command is not available in this version `　也说明不支持），你得安装其它版本（比如Ubuntu默认安装的 [vim-tiny](http://packages.ubuntu.com/trusty/vim-tiny) 版本是一个精简版不支持语法高亮，得再用  `apt-get install vim ` 安装功能比较全的版本）或者重新编译．

然后就是开启语法高亮，打开文件之后执行  `:syn on ` 即可．

如果你想默认开启此项，则可以把  `syn on  ` 放到  `~/.vimrc ` 里面（如果你从来没有编辑过  `.vimrc `, 那么我推荐你拷贝一个vim推荐配置:  `cp /usr/share/vim/vim7x/vimrc_example.vim ~/.vimrc ` ）

vim能够根据文件扩展名或者shebang (即文件头部的  `#!/usr/bin/python ` )来识别文件类型，如果没有正确识别的话，你可以用  `:set filetype=python ` 这个命令来强制vim　按python语法高亮当前文件（如果你期望vim下次打开时还是按python语法来对待当前文件，可以在文件头部或者尾部添加一行  `# vim: set filetype=python ` （这一个包含了vim选项的行在vim里面称为[modeline](http://vimcdoc.sourceforge.net/doc/options.html#modeline)））

参考: [Vim参考手册: 语法高亮](http://vimcdoc.sourceforge.net/doc/syntax.html#:syn-qstart)    [Vim参考手册: 文件类型](http://vimcdoc.sourceforge.net/doc/filetype.html#filetypes)

#### 6.2 nano

我一直以为 nano 是不支持语法高亮的，所以对于为什么Cannonical会选择nano作为默认的编辑器感到奇怪：一个这么弱的编辑器居然还．．．不过后来才发现我错了（主要是我一直只用vim和emacs，程序用其它编辑器打开文件后我就赶紧退出来设置环境变量 `EDITOR ` 了之后再进去 :-)

nano 自带了[十来种常见编程语言的语法高亮配置](http://packages.ubuntu.com/trusty/amd64/nano/filelist)，配置文件在  `/usr/share/nano ` 目录下，但需要你将它们 include 到你的  `~/.nanorc ` 才能起作用（Debian和Ubuntu在  `/etc/nanorc ` 里面已经默认包含了，但CentOS里面没有）

#### 6.3 joe

[joe](http://joe-editor.sf.net) 是我很喜欢的编辑器，因为仅仅1M的体积，但可以模拟一个微型的Emacs来用（joe可以模拟好几种编辑器: Emacs, WordStar, Pico－－而nano其实是模仿了Pico），而且屏幕上会显示常用快捷键提示，另外 `F1/F2/F3/F4 ` 调用shell很方便(这个功能需要4.0版本以上）．

当然，它还支持语法高亮，支持40多种编程语言和（这是Debian 7/8, Ubuntu 12.04/14.04和CentOS 6 (epel)里面所带的3.7版本的情况，而到了4.1版本已经增长了70多种．joe的编译安装相当简单，所以推荐使用[最新版本](https://sourceforge.net/projects/joe-editor/files/)，不想自己编译的也可以自己添加一些新的语法高亮配置: <https://github.com/cmur2/joe-syntax>．

![](https://bytebucket.org/bamanzi/notes-zim/raw/default/blog/2016/04/colorful-shell/joe.png)
