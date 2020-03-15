---
title: 物联网IoT协议之6LoWPAN快速入门教程
tags:
  - IoT
  - IPv6
  - 6LoWPAN
  - OT融合
  - 上云协议
  - 工业4.0
  - 工业互联网
  - 工业通信
  - 物联网
  - 物联网云平台
categories:
  - 物联网IoT
keywords: IPv6,6LoWPAN,IPv4,物联网IoT,工业互联网,工业4.0,物联网云平台,上云协议,万物联网
description: '什么是6LoWPAN,6LoWPAN优点,6LoWPAN简介,物联网IoT上云协议之6LoWPAN零基础快速入门教程,6LoWPAN如何影响编程,6LoWPAN的应用,6LoWPAN缺点'
date: 2020-03-08 14:34:00
---

关注我，获取更多**物联网IoT+云原生**的技术分享。

![](/images/wx.jpg)

[物联网IoT协议之mqtt快速入门教程](/2019/10/23/mqtt_guide/)
[物联网IoT协议之OPC UA快速入门教程](/2019/11/27/opc_guide/)
[物联网IoT协议之 LoRaWAN快速入门教程](/2019/12/08/lorawan_guide/)
[物联网IoT协议之NB-IoT/CoAP快速入门教程](/2019/12/18/nb_iot_guide/)
[物联网IoT上云协议之Modbus快速入门教程](/2020/01/01/modbus_guide/)
[IPv6快速入门教程](/2020/02/28/IPv6_guide/)
[物联网IoT协议之6LoWPAN快速入门教程](/2020/03/08/6LoWPAN_guide/)
[物联网IoT协议之HTTP快速入门教程](/2020/03/15/HTTP_guide/)

# 物联网IoT协议之6LoWPAN快速入门教程

## 物联网需要IPv6

下图是常规的ZigBee物联网架构图。

![常规ZigBee物联网架构图](/images/wp/6LoWPAN_ZigBee.png)

所谓万物联网，主要是万物接入基于TCP/IP的互联网/局域网，而上图中ZigBee节点并没有直接接入TCP/IP网络，应用程序和ZigBee节点之间通信，必须通过网关的中转，而**网关使用的中转通信协议并没有标准规范**，不同厂家的ZigBee网关是无法兼容的，当然，ZigBee网关和LoRaWAN网关更是不能相互替换，这会造成物联网的隔离！

互联网的繁荣是建立在统一的TCP/IP协议之上的，要想避免物联网的隔离，物联网也使用统一的TCP/IP该多好啊。

### 为什么不使用IPv4
IPv4由于地址不够用，现有的IPv4网络大量使用NAT(Network Address Translation，网络地址转换)技术。

![IPv4中的NAT](/images/wp/6LoWPAN_NAT.png)

由于NAT的存在，上图中内网A和内网B是无法直接通信的，这也是种隔离。IPv6的地址足够大，IPv6已经不需要NAT了，[IPv6快速入门教程](/2020/02/28/IPv6_guide/)中"**测试公网访问个人笔记本**"实测了IPv6是不受NAT的影响。

## 什么是6LoWPAN
6LoWPAN全称IPv6 Low Power Wireless Personal Area Network，从名字可以看出6LoWPAN的以下特点：
- 使用IPv6协议
- Low Power是低功耗
- WPAN是无线个域网

6LoWPAN是面向低功耗无线局域网的IPv6，但是IPv6标准中要求网络中所有路由器的MTU不能小于1280字节，而常规Zigbee中一帧数据最多200个字节，明显不能满足IPv6标准，所以需要一种针对低功耗无线网络的简化版IPv6，这就是6LoWPAN。

6LoWPAN协议栈如下图。

![6LoWPAN协议栈](/images/wp/6LoWPAN_stack.png)

上图中可以看到6LoWPAN的物理层和数据链路层是IEEE 802.15.4，那么什么是802.15.4呢？

### 什么是802.15.4
IEEE 802.15是电气电子工程师学会（IEEE）IEEE 802标准委员会的一个工作组，它负责制定无线个人局域网（WPAN）的协议标准。IEEE 802.15工作组根据数据传输速率、电池能耗等特点的不同，将WPAN分为不同的类型：

![802.15的不同类型](/images/wp/6LoWPAN_802.15.4.png)

上图中可以看到，大家熟悉的蓝牙使用了802.15.1，而802.15.4是传输速率最低，功耗最低的类型，**6LoWPAN选择了802.15.4，这对应了6LoPWAN的Low Power，这也限定了6LoPWAN的应用场景是低速低功耗的物联网场景**，一般都是电池供电。

ZigBee的物理层和链路层也选择了802.15.4。

### 什么是WPAN
无线个域网，是多个设备**通过无线通信组成的简单的局域网**。这涉及到两个主题：

**主题一：无线通信频段**，802.15.4标准中规定了多种频段，如：
- 868-868.6MHz频段：该频带是大多数欧洲国家的非授权频带，可以免费使用；
- 902-928MHz频段：该频带是北美，澳大利亚，新西兰等国家的非授权频带，可以免费使用；
- 779-787MHz频段：该频带是我国的非授权频带，可以免费使用；
- 2.4-2.4835GHz频段：该频带全球大多数国家的非授权频带，可以免费使用；

**主题二：组网**，802.15.4标准中，网络由多个符合标准的无线设备组成，其中**PAN协调器用来建立新的网络**，定义网络的拓扑结构，运行参数等，其他设备向PAN协调器申请加入网络。

## 6LoWPAN与IPv6的不同

### 编址
6LoWPAN中的IP地址和标准IPv6地址是一样的，但是由于128位IPv6地址占用的数据，对于低速无线网络来说太长了，6LoWPAN同时支持短地址（8-16位），可以节省数据报中的一些空间。

6LoWPAN也使用IPv6的地址自动配置，6LoWPAN可以自动生成自己的IP地址，自动生成IP的流程请参考[IPv6快速入门教程](/2020/02/28/IPv6_guide/)，加上6LoWPAN底层802.15.4的组网，6LoWPAN的编址包括以下两个步骤：

- 6LoWPAN节点设备，遵循802.15.4标准加入无线WPAN，分为手动加入或者自动搜索发现加入，成功加入WPAN网关后，节点得到PAN ID(网络ID)，然后就可以和网络中其他节点通信。
- 节点遵循IPv6地址自动配置流程，自动获得本地链路地址，再从路由器获取网络前缀，最终生成全球唯一的IPv6地址。

### 报头压缩
802.15.4属于低速网络，报头压缩可以提高通信效率。

### 分片和重组
IPv6数据报的负载最大可以有2的32次方bit，而802.15.4这种低速网络根本承载不了这么大的数据报，只能先分片再重组。

### 6LoWPAN路由器
由于6LoWPAN和IPv6是不同的协议，所以6LoWPAN路由器和IPv6的路由器不同，6LoWPAN路由器用来处理6LoWPAN协议数据

### 6LoWPAN边缘路由器
6LoWPAN设备最终也要接入标准IPv6网络，由于6LoWPAN和IPv6是不同的协议，此时需要协议的转换，这就要用到6LoWPAN边缘路由器。边缘路由器一边接入标准IPv6网络，另外一边接入6LoWPAN网络，并转换两个网络之间的数据。

![6LoWPAN边缘路由器](/images/wp/6LoWPAN_edgeR.png)

## 如何实现6LoWPAN
6LoWPAN除了要实现网络层IPv6相关的协议，还需要数据链路层802.15.4的支持，而802.15.4的实现又依赖了无线芯片，选择不同厂家的无线芯片，实现方式也会有不同。**一般芯片原厂会提供6LoWPAN协议，包括802.15.4的源码或者Demo示例**，所以6LoWPAN的实现，更多的取决于无线芯片对6LoWPAN的支持。

## 如何使用6LoWPAN

![6LoWPAN协议栈](/images/wp/6LoWPAN_stack.png)

如上图，802.15.4是物理层和数据链路层协议标准，而IPv6和6LoWPAN都是网络层，而应用程序的编程，一般都是针对应用层或者传输层的编程，所以**对于开发者来说，6LoWPAN是基础设施，基础设施建好后，开发者不需要再关心6LoWPAN，开发者使用统一的TCP/IP编程操作6LoWPAN设备**，这也是选择6LoWPAN获得的最大益处。

### 6LoWPAN选择了UDP
由于TCP需要三次握手，需要保证数据顺序，还需要支持数据重传，这些特点十分影响设备的功耗，所以6LoWPAN建议最好选择UDP作为传输层协议。不过这并不是说6LoWPAN不支持TCP，在供电、延时等没问题的情况下，基于6LoWPAN实现TCP也是可以的。

既然传输层最好选择UDP，那么应用层也只能选择基于UDP的协议了，比如COAP，COAP可以看做是轻量级的HTTP，**这也是NBIoT的思路**，请参考[物联网IoT协议之NB-IoT/CoAP快速入门教程](/2019/12/18/nb_iot_guide/)。

## 6LoWPAN的应用

物联网不少协议支持IPv6时，都不约而同的选择了6LoWPAN。
### ZigBee IP
ZigBee联盟在2013年发布了[ZigBee IP Specification](/2020/03/08/ZigBee_IP_Spec/)，使得ZigBee也能支持IPv6，ZigBee IP的体系结构为：

![ZigBee IP 体系结构图](/images/wp/6LoWPAN_ZigBee_IP.png)

上图可以看到，ZigBee是基于6LoWPAN实现对IPv6的支持。

### Thread
Thread最初由Nest公司(已被google收购)发起的低功耗无线mesh网络协议，主要用在智能家居、智能写字楼等领域，Thread的体系结构为：

![Thread体系结构图](/images/wp/6LoWPAN_Thread.png)

上图可以看到，Thread是基于6LoWPAN实现对IPv6的支持。
### MQTT-SN
MQTT是基于TCP的应用层协议，MQTT-SN(MQTT for Sensor Networks)是针对non-TCP/IP的无线传感器(比如ZigBee)的MQTT协议，MQTT-SN的体系结构为：

![MQTT-SN体系结构图](/images/wp/6LoWPAN_mqtt_sn.png)

上图可以看到，MQTT-SN是基于6LoWPAN实现无线传感器设备接入MQTT的。

## 6LoWPAN存在哪些问题

### 损失了部分性能
[On the Performance of ZigBee Pro and ZigBee IP](/2020/03/08/ZigBee_IP_Spec/)一文中，对比了ZigBee Pro和ZigBee IP的性能，下面是两者的延时测试对比。

![ZigBee IP性能测试](/images/wp/6LoWPAN_delay.png)

可见，ZigBee IP的延时相比非IP的ZigBee Pro更高，因为ZigBee IP要多一层6LoWPAN协议的转换，必然要多耗时。

### 需要额外的6LoWPAN路由器
6LoWPAN路由器和标准的IPv路由器无法替换使用，常规无线网络中需要网关，6LoWPAN网络中需要路由器，6LoWPAN网络并没有节省硬件成本。

### 当前6LoWPAN产品并不多
第一个6LoWPAN规范于2007年发布，经过这么多年的发展，目前支持6LoWPAN的产品并不多，一方面受限于IPv6的发展，一方面是因为物联网低功耗节点经常处于休眠状态，此时不需要也无法和节点通信，**只能等节点自己激活后，由节点自己上报数据，这种情况使用常规的网关或者IPv4+NAT也是没有问题的**。

6LoWPAN将来如何发展？前途是光明的，道路是曲折的。
