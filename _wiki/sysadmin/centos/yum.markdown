---
layout: wiki
title:  Yum
tags: rpm,redhat,centos
---

Package management
------------------

### View all available packages using yum list

``yum list | less ``

### List only the installed packages

``yum list installed | less ``


### Which package does a file belong to?

``# yum provides /etc/sysconfig/nfs``

Repos
-----

### List repos

``# yum repolist``

### Prevent metadata auto expiring

Yum would update repo metadata very often. To prevent this, set ``metadata_expire`` in ``/etc/yum.conf`` to '7d' or '-1'.

You can use ``yum makecache`` to force updating metadata.

[mirror - disable YUM mirrorlist checking - Unix & Linux Stack Exchange](http://unix.stackexchange.com/questions/137569/disable-yum-mirrorlist-checking)

Mirrors
-------

### fastestmirror

The fastest mirror plugin is designed for use in repository configurations where you have more than 1 mirror in a repo configuration. It makes a connection to each mirror, timing the connection and then sorts the mirrors by fastest to slowest for use by yum. 

	yum install yum-plugin-fastestmirror  # CentOS 6+
	yum install yum-fastestmirror # centos 5

configuration options in ``/etc/yum/pluginconf.d/fastestmirror.conf``

[PackageManagement/Yum/FastestMirror - CentOS Wiki](https://wiki.centos.org/PackageManagement/Yum/FastestMirror)

### Setup a mirror with reposync

[用reposync 同步YUM源到本地，搭建本地YUM源服务器 - 冷蛋蛋 - 51CTO技术博客](http://gdlwolf.blog.51cto.com/343866/1729020)


Other Tips
----------

### Download a package without installing it

from [How To Download a RPM Package Using yum Command Without Installing On Linux](http://www.cyberciti.biz/faq/yum-downloadonly-plugin/)

#### method 1:  yum install yum-downloadonly

then  ``# yum update httpd -y --downloadonly``

#### method 2: yum -y install yum-utils.noarch

then ``yumdownloader httpd``


Misc Links
----------

- [15 Linux Yum Command Examples – Install, Uninstall, Update Packages](http://www.thegeekstuff.com/2011/08/yum-command-examples/)
- [Package Management Basics: apt, yum, dnf, pkg | DigitalOcean](https://www.digitalocean.com/community/tutorials/package-management-basics-apt-yum-dnf-pkg)
