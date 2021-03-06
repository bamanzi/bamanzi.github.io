---
layout: post
title:  "Keepalived用于配置虚拟IP时的调测方法"
date:   2016-05-30 21:57:11
tags: sysadmin ha
---

Keepalived 
## 4.1 土办法检测: ssh登录VIP后通过域名等检测当前是哪台机器

不多说了。

## 4.2 arping

在第三台机器上执行 `arping` 命令，查看VIP目前绑定到了哪个MAC地址上，再将MAC地址与两台主机的IP地址相对比。（执行 `arping` 命令的机器必须在该VIP的广播范围内，否则是无法查到的。并且不能在keepalived这两台机器上查询——其实是不能在VIP MASTER上查询，而在VIP BACKUP上是可以的，只是得先弄清楚哪台是BACKUP。）

    root@SZX1000085521:~# arping 10.93.172.42                                 
    ARPING 10.93.172.42                                                       
    60 bytes from 28:6e:d4:89:86:b5 (10.93.172.42): index=0 time=1.001 sec    
    60 bytes from 28:6e:d4:89:86:b5 (10.93.172.42): index=1 time=511.242 msec 
    60 bytes from 28:6e:d4:89:86:b5 (10.93.172.42): index=2 time=940.422 msec 
    60 bytes from 28:6e:d4:89:86:b5 (10.93.172.42): index=3 time=1.001 sec

## 4.3 syslog

keepalived会采用syslog来记录日志。对于较老的系统，对应的日志文件是 `/var/log/messages` , 而 Ubuntu 14.04 上这个文件是 `/var/log/syslog` 。

    tail -f /var/log/syslog
    Feb 21 04:06:15 lb0 Keepalived_vrrp: Netlink reflector reports IP 202.54.1.1 added
    Feb 21 04:06:20 lb0 Keepalived_vrrp: VRRP_Instance(VI_1) Sending gratuitous ARPs on eth1 for 202.54.1.1

这里面的信息不多，只有关键的事件信息。如果要了解详细的信息，需要用 `keepalived -D` 启动服务（对于Debian/Ubuntu而言，是在 `/etc/default/keepalived` 里面修改配置；对于 CentOS 则是修改`/etc/sysconfig/keepalived` ），或者用后面的 `tcpdump` 方法来调试。

参考: [CentOS / Redhat Linux: Install Keepalived To Provide IP Failover For Web Cluster](http://www.cyberciti.biz/faq/rhel-centos-fedora-keepalived-lvs-cluster-configuration/)

### 示例
    
本VM开始是 `BACKUP` 状态（该VM上priority配置得比较低）
    
      May 10 10:43:17 SZX1000054493 Keepalived_vrrp[111942]: VRRP_Instance(VI_1) Entering BACKUP STATE
      May 10 10:43:17 SZX1000054493 Keepalived_vrrp[111942]: VRRP_Instance(VI_1) removing protocol VIPs.
      May 10 10:43:17 SZX1000054493 Keepalived_healthcheckers[111941]: Netlink reflector reports IP 10.93.144.58 removed
    
在MASTER VM上执行 `/etc/init.d/keepalived stop` 之后，本VM进入 `MASTER` 状态
    
      May 10 15:54:16 SZX1000054493 Keepalived_vrrp[111942]: VRRP_Instance(VI_1) Transition to MASTER STATE
      May 10 15:54:17 SZX1000054493 Keepalived_vrrp[111942]: VRRP_Instance(VI_1) Entering MASTER STATE
      May 10 15:54:17 SZX1000054493 Keepalived_vrrp[111942]: VRRP_Instance(VI_1) setting protocol VIPs.
      May 10 15:54:17 SZX1000054493 Keepalived_healthcheckers[111941]: Netlink reflector reports IP 10.93.144.58 added
      May 10 15:54:17 SZX1000054493 Keepalived_vrrp[111942]: VRRP_Instance(VI_1) Sending gratuitous ARPs on eth0 for 10.93.144.58
      May 10 15:54:22 SZX1000054493 Keepalived_vrrp[111942]: VRRP_Instance(VI_1) Sending gratuitous ARPs on eth0 for 10.93.144.58
    
在MASTER VM上重新执行 `/etc/init.d/keepalived start` 之后，由于未设置 `nopreempt` 选项，该VM重新获得 `MASTER` 地位，本VM进入 `BACKUP` 状态
    
      May 10 15:55:36 SZX1000054493 Keepalived_vrrp[111942]: VRRP_Instance(VI_1) Received higher prio advert
      May 10 15:55:36 SZX1000054493 Keepalived_vrrp[111942]: VRRP_Instance(VI_1) Entering BACKUP STATE
      May 10 15:55:36 SZX1000054493 Keepalived_vrrp[111942]: VRRP_Instance(VI_1) removing protocol VIPs.
      May 10 15:55:36 SZX1000054493 Keepalived_healthcheckers[111941]: Netlink reflector reports IP 10.93.144.58 removed


## 4.4 tcpdump


在安装keepalived的这两台机器上执行下面的命令，观察VRRP协议相关报文的输出

```sh
tcpdump -v -i eth1 host 224.0.0.18
#or: tcpdump -vvv -n -i eth0 host 224.0.0.18
```

    tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 96 bytes
    16:54:01.743275 IP (tos 0x0, ttl 255, id 451, offset 0, flags [none], proto: VRRP (112), length: 96) 10.10.28.5 > 224.0.0.18: VRRPv2, Advertisement, vrid 51, prio 103, authtype simple, intvl 1s, length 76, addrs(15): 123.12.15.2,123.12.15.3[|vrrp]
    16:54:02.744241 IP (tos 0x0, ttl 255, id 452, offset 0, flags [none], proto: VRRP (112), length: 96) 10.10.28.5 > 224.0.0.18: VRRPv2, Advertisement, vrid 51, prio 103, authtype simple, intvl 1s, length 76, addrs(15): 123.12.15.2,123.12.15.3[|vrrp]

我这边的输出实例:

        189-124-161-57.cable.cabotelecom.com.br > vrrp.mcast.net: vrrp 189-124-161-57.cable.cabotelecom.com.br > vrrp.mcast.net: 
    VRRPv2, Advertisement, vrid 51, prio 100, authtype simple, intvl 1s, length 20, addrs: 189-124-160-100.cable.cabotelecom.com.br auth "1234^@^@^@^@"
        189-124-160-136.cable.cabotelecom.com.br > vrrp.mcast.net: vrrp 189-124-160-136.cable.cabotelecom.com.br > vrrp.mcast.net:
    VRRPv2, Advertisement, vrid 50, prio 102, authtype simple, intvl 1s, length 20, addrs: 189-124-160-100.cable.cabotelecom.com.br auth "1234^@^@^@^@"

可以看出，`189.124.161.57` 和 `189.124.160.136` 都在申明虚拟IP `189.124.160.100` 的所有权，但vrid没有配置成一样，所以会两个都抢，需要将vrid改成一样才能工作。

在安装keepalived的这两台机器上执行下面的命令（其中一个），然后在其它机器上通过VIP访问服务（比如ssh, http），并行观察tcpdump的输出，看请求发到了哪端。

参考: [Verify: Keepalived IP Failover Working Or Not With tcpdump Command](http://www.cyberciti.biz/faq/linux-unix-verify-keepalived-working-or-not/)


