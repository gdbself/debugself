---
title: 车联网V2X快速入门教程
tags:
  - IoT
  - 5G
  - 车联网
  - V2X
  - 上云协议
  - 工业4.0
  - 工业互联网
  - 工业通信
  - 物联网
  - 物联网云平台
categories:
  - 物联网IoT
keywords: 5G,车联网,V2X,C-V2X,LET-V2X,5G-V2X,RSU,OBU,DSRC,MEC,物联网IoT,工业互联网,工业4.0,物联网云平台,上云协议,万物联网
description: '车联网V2X快速入门教程,什么是车联网,什么是C-V2X,什么是MEC多接入边缘计算'
date: 2020-04-25 15:50:00
---

关注我，获取更多**物联网IoT+云原生**的技术分享。

![](/images/wx.jpg)

[八大物联网IoT上云协议快速入门教程](/2020/03/23/Protocol_guide/)

# 车联网V2X快速入门教程

经常有人问，**物联网最大的应用场景是什么？**

有人说无线水表、无线电表，可以远程抄表、远程控制，可是不够fashion；

有人说共享单车，应用很广，不过自行车，总感觉不够智能；

有人说智能家居，挺潮挺智能，可是概念了一二十年，硬是没有进入寻常家庭；

经过一番研究，源码先生得出的结论，**车联网是目前能够预见的，最高大上的物联网应用**，原因无他，汽车保有量够大，汽车智能化需求够大。

**开车得集中注意力，开车累啊！**

**路太拥堵，开车浪费时间啊！**

虽然汽车自动驾驶离全面商用还远着呢，但是自动驾驶的场景很令人期待，坐进汽车后，可以放松娱乐而不是集中注意力，可以工作充电而不是浪费时间。目前，**只靠雷达和摄像头的自动驾驶，有解决不了的安全隐患**：
- 雷达只能探测到周围车辆，但是探测不到被其他车辆遮挡的部分，有各种盲区。

![车辆盲区](/images/wp/V2X_mangqu.gif)

这时候，通过车联网，车与车之间数据交换，获取到其他车辆的视野信息，盲区就消除了。

- 用摄像头识别红绿灯，随便来个雨天，还不知道能不能识别出红绿灯。

![下雨天的红绿灯](/images/wp/V2X_hld.jpg)

这时候，通过车联网获取路侧的红绿灯实时信号，精确度是100%。

## 什么是车联网
什么是车联网，弄明白以下问题就算入门了：

- 车联网，核心是车
- 车联接了谁？
- 车和他们是如何联接的？

## 车联接了谁

### 车联接了谁--V2V
V2V（Vehicle to Vehicle），即车与车之间的联接，比如一辆帕萨特，前面是一辆大卡车，帕萨特的视野被卡车遮挡，帕萨特车主心里紧张啊，通过V2V，卡车把自己的视野信息转发给帕萨特后，帕萨特车主表示情绪稳定。

### 车联接了谁--V2I
V2I（Vehicle to Infrastructure），即车与路之间联接、路上能有什么信息呢，就是红绿灯信号，路的限速等信息。

### 车联接了谁--V2P
V2P（Vehicle to Pedestrian），即车与人之间的联接，人指车主，车主控制汽车，比如输入目的地，听音乐等。

### 车联接了谁--V2N
V2N（Vehicle to Network），车与网络之间的交互，通过网络访问各种云端服务，比如交通管理中心，根据车流的整体情况，下发的实时导流信息。

### 车联接了谁--V2X
 V2X（ Vehicle to Everything），X即与车有联接的一切事物，V2X是统称。
 
![车联网V2X](/images/wp/V2X.png)
 
## V2X是如何联接的
V2X多方通信，汽车又在高速移动，**只能用无线通信**，而且最好是成熟的，稳定的无线通信方式。要说成熟稳定的无线通信，**首先想到的就是2G/3G/4G了**，它们是基于基站组成的通信网络，车联网使用类似4G网络的通信，这就是C-V2X，C是Cellular的简写（蜂窝，一个个基站组合起来像蜂窝）。

### C-V2X
C-V2X是由3GPP定义的基于蜂窝通信的V2X无线通信技术，C-V2X包含蜂窝通信和直接通信两种方式。

### C-V2X中的蜂窝通信
基于蜂窝/基站网络通信，同手机4G上网类似。4G技术应用在V2X中，称为LTE-V2X，将来5G成熟后，会平滑演进为5G-V2X。

### C-V2X中的直接通信D2D
D2D即Device-to-Device，也称为终端直接通信，两个终端（比如手机）直接通信，不需要通过基站中转；多个终端通过D2D组成分散式网路，每个终端都能发送和接收信号，并具有自动路由(转发消息)的功能，最终形成一个可以多终端相互通信的**本地网络**。

**D2D直接通信，非常适合用在车与车之间的V2V通信**，汽车在高速移动时，或者在基站覆盖信号不好时，汽车通过D2D，依旧可以和周围的其他汽车交换信息。使用D2D通信，同时也减轻了基站的通信压力。

### 关于802.11p和DSRC
全球车联网存在两大通信技术标准：
- 基于802.11p协议的DSRC（专用短程通信技术）：DSRC由欧美国家主导，不过目前欧美对DSRC也存在较大的分歧
- 基于蜂窝技术的车联网通信C-V2X：我国主推的车联网技术标准，当前阶段C-V2X发展比DSRC顺利

## 车联网产业中的参与方
如何实现V2X，车联网如何落地？初看起来很复杂，一口吃不成胖子，都是一步步来的，先修路，再搭舞台，最后就是八仙过海，各显神通。

下面简单描述当前车联网产业中参与方，弄清楚各个参与方扮演的角色，就明白了车联网这台戏是如何在演出。

### 移动运营商
V2X使用的通信协议，是经过定制和优化的4G协议和5G协议，所以V2X使用的通信基站和4G基站/5G基站还是有区别的，所以需要移动运营商对V2X的支持。

### 芯片/模组厂商
芯片/模组厂商提供天线，芯片，通信模组等硬件，基于通信模组，可以实现C-V2X的无线通信。

### RSU路侧单元提供商
RSU（Road Side Unit）是在安装在路侧的设备，RSU中要内置通信模组：

![车联网RSU](/images/wp/V2X_RSU.png)

V2I中车和路通信，主要就是和RSU通信，通过RSU获取路相关的信息。

RSU的安装密度应该非常大，密度比抓拍的电子摄像头应该大不少。

### OBU车载单元提供商
OBU(On Board Unit)是安装在车内的设备，OBU中要内置通信模组：

![车联网OBU](/images/wp/V2X_OBU.png)

OBU代表了车，车通过OBU和其他事物通信：车和车通过OBU交互，比如交换双方的车速。

OBU除了作为通信接口，还承载很多其他功能，人和车也通过OBU交互，比如车主通过语音识别控制车辆。

### 云服务提供商
云服务提供商提供各种应用，如交通应用，获取各种感知信息（摄像头信息、路侧信息、车端信息），结合交通管理信息，进行整体数据分析，进行交通引导，提高交通通行效率，预防交通违法等；

云服务还包括娱乐应用，生活便利等各种应用，范围很广。

### MEC边缘计算服务
MEC（Multi-access Edge Computing）多接入边缘计算，**将云端计算从移动核心网络下沉到接入侧（即通信基站）**，交通应用对延时和计算精度要求都非常高，使用MEC边缘计算，可以减少车联网和云端交互的延时，减轻核心网/云端的通信压力;同时，RSU由于性能不够，无法执行的计算任务，可以转移到MEC上。**举个例子，通过MEC动态获取高精度地图**：

自动驾驶中，依赖雷达是无法测量道路的上坡和下坡的，这时候需要使用高精度地图，高精度地图中记录了车道线、车道属性变化，道路的曲率、坡度、交通标志牌、路面标志等准确信息，**通过高精度地图，能让车辆准确的转向、制动、爬坡等**。

高精度地图数据量比较大，离线下载占用空间大，且还需要频繁更新地图，把高精度地图缓存在MEC边缘服务器中，车辆每到一个地方，通过车联网实时动态的获取周围、最新的高精度地图，很方便。

**MEC不但为车联网专用，随着5G的不断成熟，MEC会扩展到各行各业，如工业制造**。

如果每个基站都配套MEC，MEC的需求量会非常大，而且与当前阿里云，腾讯云这些中心化的云计算服务不同，MEC天生的分布式的，天生符合边缘计算，MEC+中心化云计算，能满足更多的场景需求，MEC市场前景可期！

MEC涉及多方合作：
- MEC硬件服务器提供商
- MEC服务/应用提供商
- MEC运营商，估计还是移动运营商为主

### 车联网安全
车联网安全很重要，犹如车辆的安全一样重要，如何防范网络攻击，如何过滤非法设备等都是重要的课题，比如类似HTTPS中的证书，车联网也需要CA证书授权(Certificate Authority)。

### 整车厂
BBA，五菱神车等车厂，主要做整合服务，把车联网服务整合到车内。

原创不易，以上内容来自源码先生个人理解，难免有错误之处，**欢迎留言或者添加源码先生个人微信号(debugself)，欢迎技术交流与合作**。

![源码先生个人微信号](/images/wp/wxgzh.png)