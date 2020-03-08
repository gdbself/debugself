---
title: 物联网IoT协议之 LoRaWAN快速入门教程
tags:
  - LoRa
  - LoRaWAN
  - 上云协议
  - 低功耗
  - 物联网
  - OT融合
  - 工业4.0
  - 工业互联网
  - 工业通信
  - 物联网云平台
url: 268.html
id: 268
categories:
  - 物联网IoT
keywords: LoRa,LoRaWAN,物联网IoT,工业互联网,工业4.0,物联网云平台,上云协议,万物联网
description: '什么是LoRa,什么是LoRaWAN,物联网IoT上云协议之LoRaWAN零基础快速入门教程'
date: 2019-12-08 22:03:36
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

# 物联网IoT协议之LoRaWAN快速入门教程
## 什么是LoRa
LoRa是一种应用在无线通信的**调频技术**，其他调频技术还有FSK/GSK、QEM/BPSK等，2009年法国公司Cycleo设计出一种优异的扩频通信算法，后来该公司被美国Semtech公司收购，于2013年推出LoRa芯片。大众熟知的蓝牙、WiFi、ZigBee等都是无线通信技术，相比这三个在上个世纪90时间年代推出的无线通信技术，LoRa是一个后来者，但是LoRa从发布到现在短短几年，火热程度已经超过了前辈们，这归功于LoRa以下优点：

- **低功耗**：物联网嘛，喜欢低功耗；
- **通信距离远**：城镇可达2-5 Km ， 郊区可达15 Km，这个距离直接把ZigBee等前辈拍在沙滩上；
- **抗干扰强**：LoRa抗干扰性能是比较优秀的，但是通信距离远，功耗又低，抗干扰又强，这三者是不可能三角，LoRa也没有这么完美，只是总体性能比较好罢了。

### LoRa节点
可以发送/接收LoRa无线信号的设备节点。

### LoRa网关
**LoRa网关和LoRa节点没有本质区别**，都是发送/接收LoRa信号的，但是网关的处理器，内存等资源更丰富，可以用来接收/发送多个节点的数据。多个节点之间相互通信，不是两两直接通信（虽然也可以直接两两通信），而是经过网关中转通信的。网关完成转发数据，统一规划信道资源等功能，拓扑结构如下图：

![LoRa网关拓扑结构](/images/wp/lora_topu_gw.png)

### 节点容量
节点容量是单个网关可以接入节点的数量，有些文章说LoRa节点容量很大，单个LoRa网关可以连接上万个LoRa节点，在源码先生看来这就是Tree New Bee了，**LoRa和LoRaWAN在节点容量上没有任何优化，和ZigBee处在同一水平，想单网关连接上万LoRa节点，只能程序员在应用层自己优化了，优化难度很大**！

## 什么是LoRaWAN
WAN是广域网的意思，多个LoRa节点可以通过同一个LoRa网关相互通信，但是如果想要跨网关通信，或者要想组成城域网/广域网，还需要云端的服务器；多个LoRa网关通过TCP/IP连接到云端，实现网关之间的通信，进一步实现更大范围内节点之间的相互通信。拓扑结构如下图：

![LoRaWAN拓扑结构](/images/wp/lora_topo_lorawan.png)

LoRaWAN定义了LoRaWAN节点，LoRaWAN网关，LoraWAN云端Server之间的通信协议，他是一套数据交互的协议标准，与mqtt(详见[物联网IoT协议之mqtt快速入门教程](/2019/10/23/mqtt_guide/))、OPC UA(详见[物联网IoT协议之OPC UA快速入门教程](/2019/11/27/opc_guide/))没有本质区别，最终目的一致，都是为了实现不同系统的数据交互。

## 基于LoRaWAN的运营商？

LoRa的速率比3G/4G低很多，但是基于LoRaWAN的广域网能力，也可以实现类似2G/3G/4G/5G的低速蜂窝网络，其中
- LoRa节点相当于手机
- LoRa网关相当于基站
- LoRaWAN相当于移动核心网

曾经有一个成为类似中国移动/中国联通的运营商的机会摆在你面前，你会不会心动？当然会！所以阿里云推出了"Link WAN物联网络管理平台"，腾讯云推出了的"LPWA 物联网络"，两者都是基于LoRaWAN的。那么LoRaWAN是否能够承载腾讯阿里成为物联网运营商的愿望呢？目前看来困难还是有的：

- LoRa使用免费的频段，**即大家都可以用的频段，这样就存在频段竞争和冲突，无法保证通信质量**；而2G/3G/4G/5G使用授权频段通信，通信频段是移动运营商独占的，不存在频段竞争和冲突，从而保证了通信质量；
- 工信部发布的《微功率短距离无线电发射设备目录和技术要求》对**LoRa的发射功率，发送数据的时长都做了限制**，大大限制了LoRaWAN的广域网能力。有人说这是为了支持NBIoT而故意打压LoRa，不过源码先生认为这个规范是非常有必要的，试想没有规范的话，免费频段大家都长时间发送大功率信号，而LoRa通信距离又很远，很容易信号相互干扰，最终是谁都通信不了！

虽然LoRaWAN的广域网能力受限，但基于LoRa优秀的低功耗远距离传输，小范围内（如工厂的厂区内）应用LoRaWAN应该还是可以的，目前LoRaWAN依旧很火！

## LoRaWAN如何实现低功耗
为了实现节点的低功耗，LoRaWAN把节点分为3种Class：

- **Class A**：节点发送数据后，开启两个接收窗口（接收窗口内可以接收网关下行给节点的数据），**除此之外的其他时间，节点都进入休眠状态，休眠状态是非常省电的**，但休眠状态时节点无法接收网关下行给节点的数据；
- **Class B**：除了Class A的功能，节点还会定时开启接收窗口，从而可以定时接收网关数据；
- **Class C**：节点会一直开启接收窗口，可以实时接收网关数据

**三种Class，功耗依次升高，但实时性也依次提升，用户根据自己的业务需求，自行选择要使用的Class**。

## LoRaWAN的通信安全
一般通过对数据进行加密来保证通信安全，而LoRaWAN提供了多层数据加密。
如下图虽然MAC Layer包含了Application Layer的数据，但MAC Layer和Application Layer使用独立的密钥分别加密，即使MAC Layer可以看到Application Layer的数据，看到的也是加密后的数据。

![LoRaWAN分层结构](/images/wp/lora_layers.jpg)

LoRaWAN中用的密钥主要有：
### NwkKey/(NwkSEncKey/ SNwkSIntKey/ FNwkSIntKey)
节点根据NwkKey生成(NwkSEncKey/ SNwkSIntKey/ FNwkSIntKey)，LoRaWAN使用(NwkSEncKey/ SNwkSIntKey/ FNwkSIntKey)加密/解密MAC层(MAC Layer)的数据。
- FNwkSIntKey：节点上行数据时，节点使用FNwkSIntKey计算上行数据的MIC校验码；
- SNwkSIntKey：节点收到云端下行的数据后，节点使用SNwkSIntKey验证下行数据的完整性
- NwkSEncKey：用来加密/解密 MAC commands

### AppKey/AppSKey
**节点根据AppKey生成AppSKey**,并使用AppSKey加密/解密应用层数据

## 节点的入网/激活
每个要接入LoRaWAN网络的节点，必须经过入网/激活的过程，**节点入网的目的是获得云端分配的DevAddr、AppSKey、NwkSEncKey等通信必须的信息**，入网方式分以下两种。

### OTAA(Over-The-Air Activation)空中激活
OTAA中，节点的AppSKey和NwkSEncKey信息是临时从云端获取的，节点中需要提前烧录NwkKey和Appkey、JoinEUI等信息，节点向云端发送Join-request message请求，云端返回Join-accept message，**节点提取Join-accept message中的DevAddr,JoinNonce等信息，并自己计算生成AppSKey和NwkSEncKey等信息**。

### ABP (Activation By Personalization)人工激活
ABP中，**节点需要的DevAddr、AppSKey、NwkSEncKey等信息，不是从云端获取的，而是提前在云端配置好，并烧录到节点固件中**，节点使用这些信息可以直接和云端通信，省去了OTAA中的Join-request流程。

## LoRaWAN的实现
LoRaWAN的标准由LoRa Alliance发布，可以从其官网下载标准文档：
- <https://lora-alliance.org/resource-hub/lorawanr-specification-v11>
- <https://lora-alliance.org/resource-hub/lorawanr-back-end-interfaces-v10>

### LoRaWAN节点的实现
LoRaWAN节点的实现请参考Semtech提供的示例代码：<https://github.com/Lora-net/LoRaMac-node>

### LoRaWAN网关的实现

#### LoRaWAN网关和LoRaWAN节点的通信
网关需要接收节点数据，以及发送数据给节点，网关通过操作网关上的LoRa芯片实现，示例代码见 <https://github.com/Lora-net/lora_gateway>

#### LoRaWAN网关和LoRaWAN Server的通信
网关需要把节点数据上报给云端的LoRaWAN Server，以及接收云端下行给节点的数据。

网关作为节点和云端Server的转发中转站，**网关不解析节点数据，也没有业务逻辑，网关起到透传的作用**，所以LoRaWAN标准只规定了节点和云端Server之间的通信协议，并没有规定网关和云端Server之间的通信协议。但是网关和云端Server要通信，还是要有通信协议的，目前常用的通信协议是semtech公司定制的packet_forwarder( <https://github.com/Lora-net/packet_forwarder/blob/master/PROTOCOL.TXT>)。

packet_forwarder使用UDP通信，**网关中需要提前配置云端的UDP Server地址和端口**。
- **网关上报数据给云端**：网关发送PUSH_DATA命令上报数据给云端
- **网关接收云端下行的数据**：网发发送PULL_DATA询问云端是否有下行数据；由于UDP是无连接的，云端LoRaWAN Server只有在收到网关PULL_DATA命令时才可以知晓网关IP，但是公网中的NAT会很快淘汰网关的IP，所以**网关必须定时发送PULL_DATA**，否则云端无法下行数据给网关。

基于packet_forwarder的使用示例见 <https://github.com/Lora-net/packet_forwarder> 。

### 有待完善的LoRaWAN Server

LoRaWAN标准规范中，把Server划分为如下几个部分

名称 | 说明
---|---
Join Server | 完成节点入网的服务器
NetWork Server | 完成节点交互的服务器
Application Server | 开放接口给第三方应用调用

其中NetWork Server 又进一步细化为

名称 | 说明
---|---
Serving NS (sNS) | 完成节点和服务器之间的MAC命令交互
Home NS (hNS) | 管理节点DevEUI、节点属性、Server属性等
Forwarding NS (fNS) | 管理网关，完成节点数据发送给指定的网关

LoRaWAN标准规范中，只定义了上面的各种Server的功能，**却没有定义各个Server交互的通信协议，更没有定义各个Server对外的API**。这造成LoRaWAN看起来有标准，但实质上云端LoRaWAN Server并没有标准可执行。当前市面上的LoRaWAN Server，除了前面提到的阿里云和腾讯云，还有TTN(https://www.thethingsnetwork.org/)、ChirpStack(https://www.chirpstack.io/)等，他们都基于LoRaWAN，而且符合LoRaWAN标准的节点理论上都可以接入这些Server，但是由于云端Server没有标准，**各家的LoRaWAN网关接入协议、应用接口都不一致，无法相互兼容**。

另外，不管是Semtech公司还是LoRaWAN联盟，都**没有提供LoRaWAN Server的实现示例**！LoRaWAN节点入网，上报数据等都必须依赖LoRaWAN Server，而官方没有提供LoRaWAN Server示例，这让那些不具备开发LoRaWAN Server的实力的小公司，想用LoRaWAN却用不了，只能抛弃LoRaWAN而使用自定义协议，而自定义协议相比LoRaWAN，有以下优点：

- LoRaWAN协议比较复杂，自定义协议可以很简单，上手快
- LoRaWAN中必须有LoRaWAN Server，自定义协议可以不依赖LoRaWAN Server，省去云端Server部署，也节省成本

至此，源码先生不得不说，LoRaWAN Server还有很多地方有待完善，源码先生一度怀疑官方根本没有花精力去推广LoRaWAN生态啊？！

### LoRaWAN Server的开源实现
ChirpStack(https://www.chirpstack.io/)是一个开源的LoRaWAN Server实现，基于非常宽松的MIT协议，ChirpStack之前叫loraserver(https://www.loraserver.io/)，作者说是LoRa被Semtech注册为商标了，所以在2019年11月份改名为ChirpStack。

![ChirpStack 原loraserver架构图](/images/wp/lora_architecture.png)

## LoRaWAN之Hello World
下面以ChirpStack(原loraserver)的安装和使用作为LoRaWAN的Hello World吧。

### 安装ChirpStack(原loraserver)
chirpstack官网(https://www.chirpstack.io/overview/)提供了多种安装的方式，这里选择安装步骤最简单的Docker Compose方式。

以Ubuntu系统为例，首先要安装docker和Docker Compose（docker已经是云端开发的基础设施，不了解docker的话，有必要借此学习下docker的用法）。

```
sudo apt-get install docker
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

下载chirpstack对应的docker-compose.yml
```
git clone https://github.com/brocaar/chirpstack-docker.git
cd chirpstack-docker
```
为了在国内使用，需要把chirpstack-docker\configuration\chirpstack-network-server\chirpstack-network-server.toml中的EU_863_870修改为CN_470_510，**这是国内的免费频段**，并注释掉所有extra_channels，如下图：

![ChirpStack docker-compose配置](/images/wp/lora_ns_config.png)

启动chirpstack

```
docker-compose up -d
```

### 使用ChirpStack(原loraserver)
通过http://you_host_ip:8080/访问chirpstack的Web界面，用户名和密码默认都是admin。

首先要添加Network-servers，如下图。

![添加Network-servers](/images/wp/lora_add_ns.png)

填入如下图的信息，提示添加Network-servers成功。

![添加Network-servers](/images/wp/lora_add_ns2.png)

*chirpstack-network-server:8000是docker内部的域名地址(chirpstack-network-server这个名字是docker-compose.yml中配置的)，此处docker部署，必须填chirpstack-network-server:8000。*

添加Service-profiles，如下图。

![添加Service-profiles](/images/wp/lora_add_server.png)

填入如下图的信息，提示添加Service-profiles成功。

![添加Service-profiles](/images/wp/lora_add_server2.png)

添加Device-profiles，如下图。

![添加Device-profiles](/images/wp/lora_add_device_pro.png)

![添加ABP Device-profiles](/images/wp/lora_add_abp_profile.png)

在Device-profiles中，需要指定使用的入网方式，上图中默认为ABP的入网方式，如果要修改为OTAA的方式，可参考下图。

![添加OTAA Device-profiles](/images/wp/lora_add_otaa_profile.png)

添加Applications，如下图。

![添加Applications](/images/wp/lora_app_add.png)
![添加Applications](/images/wp/lora_app_add2.png)

*注意，上面的Network-servers、Service-profiles、Application等概念是ChirpStack(原loraserver)对LoRaWAN的实现方式，但LoRaWAN标准中并没有规定Network-servers、Service-profiles等概念。*

选择刚才创建的App，点击进入。

![进入Applications](/images/wp/lora_dev_add0.png)

进入App界面后，可以看到添加节点/Devices的按钮。

![添加LoRa节点](/images/wp/lora_dev_add.png)
![添加LoRa节点](/images/wp/lora_dev_add2.png)

上面添加的是ABP节点，APB节点需要手动激活(即入网)，具体激活方式见下图。

![激活ABP LoRa节点](/images/wp/lora_dev_add3.png)

云端配置完成，把DevEUI、AppSKey等信息烧录到节点，节点通过网关上报数据，可以在chirpstack网页上查看上报的数据。

![查看LoRa节点数据](/images/wp/lora_dev_data.png)

### 应用程序集成ChirpStack(原loraserver)
应用程序如何从LoRaWAN Server获取节点的数据，如何发送命令给节点呢？LoRaWAN标准中规定了Application Server，第三方应用程序通过Application Server的接口，可以读取LoRaWAN节点的数据，可以发送数据给LoRaWAN节点。

ChirpStack(原loraserver)的Application Server提供了多种形式的接口。
#### RESTful JSON API
RESTful风格的HTTP API，访问http://you_host_ip:8080/api可以查看所有HTTP API，应用程序通过HTTP，可以调用ChirpStack的所有功能，包括

- Network-servers的增删改查
- Service-profiles的增删改查
- Device-profiles的增删改查
- Applications的增删改查
- 节点(设备)的增删改查
- 读取节点数据
- 给节点发送命令数据

#### mqtt
相比HTTP，mqtt使用长连接，实时性更好。通过mqtt，可以实现如下功能
- 获取节点数据：订阅主题'application/[applicationID]/device/[devEUI]/rx'可以收到节点上报给云端的数据
- 给节点发送数据命令：通过向主题'application/[applicationID]/device/[devEUI]/tx'发布消息，可以给节点发送数据

具体mqtt消息格式见 <https://www.chirpstack.io/application-server/integrate/sending-receiving/mqtt/>

#### gRPC
见 <https://www.chirpstack.io/application-server/integrate/grpc/>
