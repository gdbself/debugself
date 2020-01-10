---
title: 集群和分布式的异同/区别
tags:
  - 分布式
  - 区别
  - 集群
  - 微服务
  - 云原生
url: 233.html
id: 233
categories:
  - 微服务
keywords: 分布式,区别,集群,微服务,云原生
description: '集群和分布式有什么区别'
date: 2018-08-08 23:54:05
---

* 集群是从整体的角度说的，整体由多台机器组合而成，每台机器功能完全一样，一台机器挂了不影响整体，整体上可以达到高可用的目的
* 分布式是从内部的角度说的（相对中心化来说），一个整体功能分布到不同的机器上，每个机器完成不同的功能，一台机器挂了会影响整体；分布式的并行执行加速了整体功能的速度
* 集群通常也是分布式，从集群内部看，多台机器之间的同步/通信属于分布式；
* 分布式内部，可以使用集群，也可以不使用集群
    
**其他举例**

p2p网络，从内部看，属于分布式；

区块链也是p2p网络，从内部看，属于分布式的，且整体提供同样的功能，整体上也是集群

redis cluster和redis replication，从整体上看，都是集群；从内部看，都算属于分布式

  

**其他参考**

redis cluster

[http://doc.redisfans.com/topic/cluster-tutorial.html](http://doc.redisfans.com/topic/cluster-tutorial.html)

redis replication

[http://doc.redisfans.com/topic/replication.html](http://doc.redisfans.com/topic/replication.html)