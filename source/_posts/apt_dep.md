---
title: apt-get循环依赖的解决方法
tags:
  - apt-get
  - Linux
  - Ubuntu
url: 133.html
id: 133
categories:
  - Linux
  - Ubuntu
date: 2018-01-17 00:23:41
---

解决The following packages have unmet dependencies；Unmet dependencies. Try 'apt-get -f install' with no packages (or specify a solution).错误的方法

为了安装最新版本的Erlang/OTP，从erlang官网下载了.deb文件，然后执行

dpkg -i esl-erlang\_20.0-1\_ubuntu\_trusty\_amd64.deb

提示缺少libsctp1等组件；

于是我执行apt-get install libsctp1，结果提示缺少libwxgtk等组件；

于是我执行apt-get install libwxgtk3.0，结果又提示我缺少libsctp1；

#dpkg -i esl-erlang\_20.0-1\_ubuntu\_trusty\_amd64.deb的提示
dpkg: dependency problems prevent configuration of esl-erlang:
esl-erlang depends on libwxbase2.8-0 | libwxbase3.0-0 | libwxbase3.0-0v5; however:
Package libwxbase2.8-0 is not installed.
Package libwxbase3.0-0 is not installed.
Package libwxbase3.0-0v5 is not installed.
esl-erlang depends on libwxgtk2.8-0 | libwxgtk3.0-0 | libwxgtk3.0-0v5; however:
Package libwxgtk2.8-0 is not installed.
Package libwxgtk3.0-0 is not installed.
Package libwxgtk3.0-0v5 is not installed.
esl-erlang depends on libsctp1; however:
Package libsctp1 is not installed.

#执行apt-get install libsctp1的提示
The following packages have unmet dependencies:
esl-erlang : Depends: libwxbase2.8-0 but it is not going to be installed or
libwxbase3.0-0 but it is not going to be installed or
libwxbase3.0-0v5 but it is not installable
Depends: libwxgtk2.8-0 but it is not going to be installed or
libwxgtk3.0-0 but it is not going to be installed or
libwxgtk3.0-0v5 but it is not installable
Recommends: erlang-mode but it is not going to be installed
E: Unmet dependencies. Try 'apt-get -f install' with no packages (or specify a solution).

#执行apt-get install libwxgtk3.0的提示
The following packages have unmet dependencies:
esl-erlang : Depends: libwxbase2.8-0 but it is not going to be installed or
libwxbase3.0-0 but it is not going to be installed or
libwxbase3.0-0v5 but it is not installable
Depends: libsctp1 but it is not going to be installed
Recommends: erlang-mode but it is not going to be installed
libwxgtk3.0-0 : Depends: libwxbase3.0-0 (>= 3.0.0) but it is not going to be installed
libwxgtk3.0-0-dbg : Depends: libwxbase3.0-0-dbg (= 3.0.0-2) but it is not going to be installed
libwxgtk3.0-dev : Depends: wx-common (= 3.0.0-2) but it is not going to be installed
Depends: wx3.0-headers (= 3.0.0-2) but it is not going to be installed
Depends: libwxbase3.0-dev (= 3.0.0-2) but it is not going to be installed
E: Unmet dependencies. Try 'apt-get -f install' with no packages (or specify a solution).

  

你妹这是逗我玩呢，还能循环依赖？这不死锁了嘛。。。

按照提示执行apt-get -f install，依旧同样的错误！

好吧，我不安装Erlang/OTP还不行吗？然而，apt-get命令直接不能用了，比如我执行

apt-get install htop

还是同样的错误提示，apt-get命令不能用，那ubuntu相当于废掉了。。。

  

过了几天，不知道什么灵感来了，忽然明白了错误原因：安装Erlang/OTP时发现缺少依赖，而依赖未安装成功，于是安装只进行了一半就中断了，错误提示也被遗留在apt系统中。。。解决方法就是先清理掉这个遗留的错误，于是执行

#删除安装一半的Erlang/OTP
apt-get remove esl-erlang

然后再安装libsctp1、libwxgtk3.0等依赖，这次就没有循环依赖的提示了；继续执行

dpkg -i esl-erlang\_20.0-1\_ubuntu\_trusty\_amd64.deb

Erlang/OTP安装成功，哦耶。