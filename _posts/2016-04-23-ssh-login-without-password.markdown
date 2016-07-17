---
layout: post
title:  "如何配置ssh免密码登录"
date:   2016-06-1 22:17:11
tags:   [ssh putty]
---

[TOC]

如果你在管理一堆unix机器，每次登录都要输入密码是挺烦的事情，一方面为了安全我们一般不会将所有机器的密码都设置成一样，另一方面就算一样每次都输入一遍也很麻烦。

这种情况下我们一般是用ssh key来代替密码鉴权，也就是无密码登录了。这在scp/sftp传送文件、rsync同步文件、sshfs 映射远端目录时都能带来很大的便利。（另外，通过ssh方式读写github的git库其实也基本是这个原理，只不过提交public key给对方让对方信任是在web界面上进行的。）

### 1. 基本用法

#### 1.1 基本说明
ssh key是一对密钥文件，一个public key文件是要给放到多端让其加到信任列表的，一个private key是留存本地，在鉴权的时候才需要。

下面的详细说明来自 [SSH 安全性和配置入门](http://www.ibm.com/developerworks/cn/aix/library/au-sshsecurity/):

> 为了帮助验证身份，SSH 有一个密钥管理功能和相关的代理。当配置为公钥身份验证时，您的密钥证明您在远程 SSH 主机上的身份。一个基于 SSH 的身份包括两个部分：一个公钥和一个私钥。私有 SSH 密钥是用于出站 SSH 连接的用户身份，且应当保密。当用户发起一个 SSH 或 SCP 会话到远程主机或服务器时，他或她被认为是 SSH 客户端。通过一个数学算法，一个私钥如同您的电子身份证；公钥如同您向其出示身份证的锁或门机制。您的私钥说，“这真的是 Fred Smythe”；公钥说，“是的，您确实是真正的 Fred Smythe；您已通过身份验证：请进入。”
>
> 您的公钥代表您允许通过您的大门或锁进入的人。公钥需要保密；它们不能用于泄漏一个系统或对系统进行未经授权的访问。在一个 Linux 或 UNIX 系统上，这些私有和公共密钥对存储在 ASCII 文本系统中；在 Windows 系统上，一些程序将密钥对存储为文本文件，一些存储在 Windows 注册表中。
	
> ![](http://www.ibm.com/developerworks/cn/aix/library/au-sshsecurity/SSH_public_private_key.gif) 


#### 1.2 生成密钥对

		[fsmythe@example.com ~]$ /usr/bin/ssh-keygen -t dsa
		Generating public/private dsa key pair.
		Enter file in which to save the key (/home/fsmythe/.ssh/id_dsa):
		Enter passphrase (empty for no passphrase): ******    (Enter 'mypassword')
		Enter same passphrase again: ******	(Enter 'mypassword')
		Your identification has been saved in /home/fsmythe/.ssh/id_dsa.
		Your public key has been saved in /home/fsmythe/.ssh/id_dsa.pub.
		The key fingerprint is:
		33:af:35:cd:58:9c:11:91:0f:4a:0c:3a:d8:1f:0e:e6 fsmythe@example.com
		[fsmythe@example.com ~]$


* 密钥有多种类型（DSA, RSA, ECDSA, ED25519等），上面用的是DSA，不指定类型时ssh-keygen默认类型是RSA．
* 我们可以生成多个密钥，每个保存在不同的文件中．本例中生成的密钥保存在 `/home/fsmythe/.ssh/id_dsa` 和  `/home/fsmythe/.ssh/id_dsa.pub` 中（前者是私钥，后者是公钥）
* Passphrase也是一种密码，是在程序读取你的私钥文件时要用到的（即你的私钥文件被加密保存了）．如果你想完全自动登录对端（不想交互式输入任何东西）那么这里可以不输入passphrase（直接回车），不过从安全性上面来说并不是太好（更好的办法是采用 `ssh-agent` 来加载你的密钥（加载时输入passphrase），然后在后面使用过程中就是 ssh-agent 与对端交互，不再需要输入passphrase了）



#### 1.3 配置自动登录

要用这个ssh key自动登录另一个机器的话，需要在本机执行这个：

	    ssh-copy-id -i ~/.ssh/id_rsa_xxx.pub johndoe@210.32.142.88

（当然，这一次还是要输入密码的．如果你生成密钥时输入了passphrase的话，这里还得输入passphrase）

这样下次就可以直接用 `ssh johndoe@210.32.142.88` 来直接登录对端机器了．当然 `scp johndoe@210.32.142.88:/home/johndoe/.bashrc . `也不会再询问你密码，`rsync -av johndoe@210.32.142.88:/h[[ome/johndoe/Downloads]] .` 也不会．

#### 1.4 参考文档：


* [如何在 CentOS / RHEL 上设置 SSH 免密码登录](http://linux.cn/article-6901-1.html) （其实内容并不只是适用于 RHEL/CentOS，甚至连 RHEL/CentOS　上典型的selinux的问题（见本文后面的补充说明）都没有提到）
* [SSH 安全性和配置入门](http://www.ibm.com/developerworks/cn/aix/library/au-sshsecurity/): ( 跟我们这里相关的是中间＂[SSH 的私钥和公钥对](http://www.ibm.com/developerworks/cn/aix/library/au-sshsecurity/#key-pairs)＂  和 ＂[配置公私 SSH 密钥对的步骤](http://www.ibm.com/developerworks/cn/aix/library/au-sshsecurity/#public-private)＂这两节 )


### 2. 各种细节问题

#### 2.1 目录权限问题导致ssh key不被接受

如果你自动登录不成功，在屏幕上见到如下字样:

	$ ssh admin@210.32.142.88
	@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
	@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
	@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
	Permissions 0755 for '/home/johndoe/.ssh/id_rsa' are too open.
	It is required that your private key files are NOT accessible by others.
	This private key will be ignored.
	bad permissions: ignore key: /home/johndoe/.ssh/id_rsa
	admin@210.32.142.88's password:

这里的文字已经把原因说得比较清楚了，是 `/home/johndoe/.ssh/id_rsa` 的权限设置得太宽泛，ssh认为密钥文件可以被其它人读取／拷贝，所以拒绝使用它．解决办法是去除其它人的读写权限（`chmod go-rw ~/.ssh/id_rsa` ）－－当然，前提是你确认这个文件没有被被人盗用（或者你不在乎这个）．

#### 2.2 RHEL/CentOS的selinux干扰导致登录不成功

对RHEL6服务器配置ssh key自动登录死活不成功，ubuntu就一点问题没有，结果是SELinux在搞鬼，在你排除了其它明显的原因后可以试试这一句（在对端上（即RHEL/CentOS上）执行）：

	  restorecon -Rv /home/myname/.ssh 

参考:  [Can't get SSH public key authentication to work - Server Fault](http://serverfault.com/questions/55343/cant-get-ssh-public-key-authentication-to-work/362011#362011)

#### 2.3 没有ssh-copy-id时如何手工设置

也许你会好奇 `ssh-copy-id` 到底干了什么，或者你的系统上没有这个工具（后面我们将putty key加到openssl信任列表时就会需要了解这个）．

其实挺简单，它只是将你的public key 加了对端的 `~/.ssh/authorized_keys` 这个文件中（每条密钥一行）．

不过这里也有一个细节:  对端的 `~/.ssh` 目录和 `~/.ssh/authorized_keys` 文件均不能是其它人可以写入的（即为了防止其它人写这个文件来达到登录当前帐号）．

所以 `ssh-copy-id` 的比较完整的手工设置方法是：  

	$ ssh username@www-03.nixcraft.in "umask 077; mkdir .ssh"
	$ cat $HOME/.ssh/id_rsa.pub | ssh username@www-03.nixcraft.in "cat >> .ssh/authorized_keys"

参考:  [Install / Append SSH Key In A Remote Linux / UNIX Servers Authorized_keys](http://www.cyberciti.biz/faq/install-ssh-identity-key-remote-host/)

#### 2.4 多个服务器需要用不同的ssh key登录

前面说过，我们可以生成多个密钥，每个保存在不同的文件中．`ssh-keygen` 会询问你保存的位置，你也可以对密钥文件改名（只要两个文件的基本名一致即可）．

登录某个服务器时如何指定具体的密钥呢？


```sh
	ssh -i ~/.ssh/id_rsa_inner  admin@210.32.151.66
	scp -i [[~/.ssh/id_rsa_inner]]  admin@210.32.151.66:/home/admin/.bashrc .
	ssh-copy-i -i [[~/.ssh/id_rsa_inner]] admin@210.32.151.66
```

如果你觉得这样比较繁琐，或者像rsync这样的工具并没有提供类似 `-i` 选项让你指定密钥文件，那么可以配置 `~/.ssh/config` 文件来解决

	Host 210.32.151.66
	   IdentityFile ~/.ssh/id_rsa_inner
	   UserName admin
	
	Host bbs1
	  HostName  210.32.142.88
	  IdentityFile ~/.ssh/id_rsa_bbs
	
	Host 10.93.*
	  IdentityFile ~/.ssh/id_rsa_group

这种情况下，登录不同的地址就会自动采用不同的密钥了．

参考: 

* [OpenSSH Config File Examples](http://www.cyberciti.biz/faq/create-ssh-config-file-on-linux-unix/)
* [ssh_config manpage](https://manpages.debian.org/cgi-bin/man.cgi?query=ssh_config&apropos=0&sektion=0&manpath=Debian%208%20jessie&format=html&locale=en)


#### 2.5 设置默认用户名

不指定用户名时，ssh默认采用当前本地用户名，试图连接对端同名用户。指定的方法有两种：

	  ssh user@host
	  ssh -l user host

第一种方法更常用一些，比如rsync, scp等均支持．

不想在命令行里面指定的，也可以编辑 `~/.ssh/config`，在相应的 `Host` 下面配置 `UserName` 即可（参考上一节的例子）．

#### 2.6 putty怎么无密码登录

这里有几个问题: 

1. 如何指定用哪个key来登录某个服务器；
2. PuTTY的密钥生成工具puttygen所生成的密钥文件是自己的格式（一般只需要保存私钥文件，扩展名为 `ppk`，其实里面包含了公钥数据和私钥数据．puttygen并不强行要求你单独保存 public key文件）．
3. PuTTY并没有提供类似 `ssh-copy-id` 这样的工具，要将自己的public key加到对端的信任列表里面得自己动手


这几个问题的答案是:


1. 在 Session　的设置界面里面 `Connection->SSH->Auth->Private key file for authentication` 指定，它只接受它自己的 ppk 格式

![](https://www.howtoforge.com/images/ssh_key_based_logins_putty/16.png)

如果你的key只有少数几个，都要用来登录很多个服务器，那么不必在所有session里面一个个地设置，可以预先用 pageant 工具加载你的常用ppk，然后putty登录时会自动跟pageant联系，只有用里面的key登录失败之后才会让你输入密码（这与openssh里面ssh-agent原理差不多）

2. 用puttygen工具打开ppk文件，即可看到对应的public key；也可以将openssh的`ssh-keygen`生成的私钥转换成putty ppk（在puttygen工具的菜单 `Conversions->Import key`）

3. 自己用上面的方法将public key写入对端的 `.ssh/authorized_keys`　文件（每个key一行，注意保证目录和文件权限均是700）


详细说明: [ Key-Based SSH Logins With PuTTY - Page 3](https://www.howtoforge.com/ssh_key_based_logins_putty_p3%20)

#### 2.7 passphrase与ssh agent

前面说过生成ssh key的时候，从安全的角度来讲，是应该指定 passphrase 的，不过这样一来，每次用这个key登录服务器的时候，都需要输入一遍 passphrase，这比较烦人．

解决的办法就是用ssh agent（putty的对应工具叫 pageant），它负责加载你的ssh key，你只在这个时候需要输入 passphrase，然后ssh/scp这些工具都是跟ssh agent联系了．

原理并不复杂，但openssh的ssh agent用起来还是有点麻烦的 (想了解的可以阅读：SSH 安全性和配置入门 : [配置和使用 ssh-agent](http://www.ibm.com/developerworks/cn/aix/library/au-sshsecurity/#ssh-agent) )，所以你可以从putty的pageant开始了解如何使用，因为你只需要在图形界面操作几下而已，详细说明请阅读：[Let Pageant Remember Your Key Passphrase](https://www.howtoforge.com/ssh_key_based_logins_putty_p4#-let-pageant-remember-your-key-passphrase)．你掌握了putty pageant的用法之后再来看openssh agent就比较简单了． (P.S. putty其实也是有linux版本的）


### 3. 其它参考文档


* [SSH keys - ArchWiki](https://wiki.archlinux.org/index.php/SSH_keys)
