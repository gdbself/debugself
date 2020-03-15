---
title: 物联网IoT协议之NB-IoT/CoAP快速入门教程
tags:
  - CoAP
  - IoT
  - NB-IoT
  - 上云协议
  - 低功耗
  - 物联网
  - OT融合
  - 工业4.0
  - 工业互联网
  - 工业通信
  - 物联网云平台
url: 276.html
id: 276
categories:
  - 物联网IoT
keywords: NB-IoT,物联网IoT,工业互联网,工业4.0,物联网云平台,上云协议,万物联网
description: '什么是NB-IoT,物联网IoT上云协议之NB-IoT零基础快速入门教程'
date: 2019-12-18 00:06:33
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

# 物联网IoT协议之NB-IoT/CoAP快速入门教程

## 什么是NB-IoT
如果说NB-IoT是窄带物联网，再来个NB-IoT的部署图，可能直接把入门者弄懵，其实NB-IoT是一个比较容易理解的技术，来，直接上图。

![NB-IoT物联网卡](/images/wp/nb_sim.png)

没错，这就是地球人都知道的SIM卡，简单的说，**NB-IoT就是低配版的2G/3G/4G/5G**，他们有很多相似之处:

- 使用运营商的基站通信，得给运营商交月租或者流量费
- 可以发短信
- 可以上网

但是**NB-IoT卡不支持语音通话**，毕竟是“窄带物联网”，速率比较慢，NB-IoT最高速率也就250kbps,和5G相比，只能说是丐版5G了。

### 使用NB-IoT替代2G
实际上，NB-IoT使用的通信频段就是2G的通信频段。

![NB-IoT通信频段](/images/wp/nb_Hz.jpg)

从上图可以看到，电信的NB-IoT频段全部集中在870-885MHz，这个频段就是之前电信的2G频段，电信2G用户很少，所以电信的2G直接全部退网了，把所有2G频段都转给NB-IoT使用了；电信的NB-IoT通信频段比移动和联通都有优势，因为通信频率越低，信号衰减越小，传输距离越远，基站部署成本越低；不过电信比较任性，使用电信的NB-IoT卡，必须绑定电信的IoT云平台，使用不太方便。移动由于2G用户较多，全部退网2G影响比较大，移动只能把部分2G频段转给NB-IoT，还有部分频段继续用在2G。**随着将来2G全部退网，NB-IoT会全面替换2G**。

### NB-IoT的特点和使用场景
NB-IoT能全面替换2G，那一定有过人之处了。NB-IoT是为了适用物联网需求专门定制的，相比2G，NB-IoT具有以下特点：

- 低功耗：物联网很多设备只能电池供电，更喜欢低功耗，下文会进一步说明NB-IoT是如何实现低功耗的；
- 低速率：速率高了耗电高，为了低功耗，只能牺牲点速率了；
- 大连接：当前2G/3G/4G网络，单个基站能够接入的手机终端个数其实并不多，在人多的密集场合，还是会出现手机没有信号的情况（不要说自己没遇到过，那是因为人还不够多）。物联网的特点之一是设备数量巨多，而NB-IoT设计的目标就是海量连接，NB-IoT能够实现海量连接不是因为NB-IoT的技术有多NB，而是利用了物联网设备很多时候都在休眠的特性，休眠的设备根本就没占用连接嘛；
- 低成本：NB-IoT整个生态，包括NB-IoT模组，NB-IoT流量收费，NB-IoT基站都是奔着低成本设计的；
- 强覆盖：NB-IoT信号覆盖比2G强，别问为什么强，问就是源码先生也不太懂。

基于NB-IoT以上特点，NB-IoT比较适合节点数量多，但传送数据量不大，功耗低(电池供电)的场景，比如路灯、水表，烟感，共享单车等；当然供电不是问题的场景，使用NB-IoT也是可以的，还可以获得更好的实时性。

## NB-IoT之Hello World
**使用NB-IoT，主要就是使用它上网的功能，也就是TCP/UDP通信**。这里的Hello World是实现NB-IoT的UDP通信，即NB-IoT作为UDP客户端，发送数据到UDP服务器，并接收服务器返回的消息，准备材料如下

- NB-IoT模组一个(BC28模组)
- NB-IoT卡一张(移动的卡)
- 本地PC电脑一台+串口调试软件
- USB转串口(TTL)线
- 具有公网IP的电脑一台(阿里云ECS)

把NB-IoT卡插入NB-IoT模组

![NB-IoT BC28模组](/images/wp/nb_bc28.png)

然后通过AT操作/控制NB-IoT模组（AT指令是以回车结尾的字符串，大多数通信模组如NB-IoT模组、4G模组都支持以AT指令的方式操作模组）；PC机通过USB转串口（TTL）连接NB-IoT模组的RX、TX、GND引脚，在PC运行“串口调试软件”打开COM口，可以发送AT指令给NB-IoT模组。

![串口调试软件](/images/wp/nb_com.png)

因为NB-IoT使用的是运营商的网络，运营商的网络是公网访问的，所以**必须建立公网UDP服务器**才行。这里用阿里云ECS服务器（Ubuntu系统），在服务器运行如下命令可以建立UDP服务器。

```
nc -v -l -u 20000    #启动UDP服务器，端口20000
```

BC28模组建立UDP套接字，并上报数据到UDP服务器的AT指令如下
```
AT+QREGSWT=2    //Disable registration function of Huawei’s IoT platform
OK
…               //Connect to network
AT+CGPADDR      //Query the IP address of the module
+CGPADDR:0,10.3.42.109
OK
AT+NSOCR=DGRAM,17,0,1 //创建UDP套接字
1
OK
AT+NSOST=1,220.180.239.212,20000,5,48656c6c6f,100 //向阿里云IP和20000端口发送UDP数据，48656c6c6f是Hello的hex编码
1,5
OK
+NSOSTR:1,100,1     //射频信号发送成功
```

在服务器的nc命令中，可以看到收到来自NB-IoT的Hello字符串；

![nc建立UDP服务器](/images/wp/nb__udp_hello.png)

服务器下行数据给NB-IoT设备，在nc命令行手动输入"World"并回车

![nc回复数据](/images/wp/nb_udp_world.png)

NB-IoT模组会实时收到服务器的回应
```
+NSONMI:1,5         //串口调试工具会自动显示收到服务器的回应
```

输入AT指令读取服务器回应的内容

```
AT+NSORF=1,5        //Read the message
1,220.180.239.212,8012,5,576f726c64,0   // 576f726c64是World的hex编码
OK
```

## NB-IoT低功耗的实现原理
**所谓的低功耗，本质都是靠通信模组的休眠实现的**，因为休眠时基本是不耗电的，LoRaWAN、NB-IoT莫不如此。NB-IoT有三种模式支持不同的场景：
### PSM（Power Saving Mode），即省电模式
PSM模式NB-IoT模组处于休眠状态，当设备没有数据要发送时，模组自动进入PSM状态；当设备有数据要发送时，模组自动退出PSM状态，然后执行发送任务，发送完毕后再自动进入PSM状态。PSM模式下，模组需要配置的关键参数有：

- Active-Time：当模组发送数据完毕，经过Active-Time时间后，模组自动进入PSM状态；Active-Time可取值范围0-1860秒；
- Periodic-TAU：Periodic tracking area updating是基站/运营商核心网要求的NB-IoT周期上报的数据包，类似心跳包，如果NB-IoT设备长久休眠且不上报数据，移动核心网就无法知道NB-IoT设备的状态了，所以基站/运营商核心网要求NB-IoT设备每隔TAU时间，必须上报依次自己的状态。3GPP协议规定Periodic-TAU默认为54min，最大可达310hr。

Active-Time和Periodic-TAU这两个参数，完全由NB-IoT设备自行配置吗？不是，**这两个值是设备端和运营商核心网协商决定的**，一般是NB-IoT设备先上报自己期望的Active-Time和Periodic-TAU值给核心网，核心网再根据当前网络整体状况权衡，并下发最终的Active-Time和Periodic-TAU给NB-IoT设备。

PSM模式下，有以下要点：

- **当设备进入PSM状态，无法接收服务器下行的数据**，所以PSM模式只适用于设备主动上报数据的场景（如水表电表），不适用于服务端实时控制设备的场景（如共享单车）
- 设备上报数据后的Active-Time时间内，可以认为设备处于连接状态，此时服务器下发数据给设备，设备是可以接收到的
- **设备建立TCP连接后进入PSM状态，TCP连接依旧不会失效**，下次发送TCP数据时，不需要手动再次建立TCP连接，可以直接发送TCP数据，这是因为虽然设备进入PSM状态，但是核心网依然保存着TCP连接所有的上下文数据
- 当设备处于PSM状态时，如果要上报数据，可以直接使用AT命令发送数据，模组会自动退出PSM，不需要手动发送“激活”指令

### DRX(Discontinuous Reception)模式
DRX模式下，**NB-IoT设备每隔DRX周期会主动监听一次寻呼信道，检查是否有下行业务到达**，而且DRX周期比较短（取值范围为：1.28s，2.56s，5.12s 或者10.24s），所以DRX模式下的设备可以实时接收服务器下发的命令。由于NB-IoT模组需要不停监听寻呼，这种模式比较耗电。

![NB-IoT DRX](/images/wp/nb_DRX.png)

**DRX周期一般由运营商核心网决定**，核心网会下发DRX周期给NB-IoT设备，设备按照DRX周期监听寻呼即可。

### eDRX(extended DRX)模式
eDRX是DRX模式的扩展，**它是PSM和DRX的组合，兼顾低功耗和实时性**。

![NB-IoT eDRX](/images/wp/nb_eDRX.png)

如上图，每个eDRX的周期的开始时候，有一个PTW（寻呼时间窗口），在PTW时间内，设备处于同DRX一样的模式，超过PTW时间内，设备处于同PSM一样的模式。
另外，运营商核心网在PTW窗口外接收到下行数据包，会缓存数据包（对每个设备只能缓存一条），进入PTW时间窗口内时再转发数据包给NB-IoT设备。

## NB-IoT常用的应用层协议CoAP
前面的Hello World简单演示了NB-IoT使用UDP通信的流程，但是基于原生的UDP构建应用程序会很麻烦，一般还需要定义应用层协议，NB-IoT常用的应用层协议是CoAP（官网：https://coap.technology）。CoAP（Constrained Application Protocol）按照字面理解，这是专门为资源受限的IoT设备定制得应用层协议，它的传输层默认使用UDP，UDP比TCP更省资源，且IoT设备休眠后会影响TCP的长连接，UDP更适合IoT场景。

CoAP和HTTP有很多相似之处，比如访问资源的URI和大家熟知的网址格式一致：

```
coap-URI = "coap:" "//" host [ ":" port ] path-abempty [ "?" query ]
coaps-URI = "coaps:" "//" host [ ":" port ] path-abempty[ "?" query ]
```

CoAP也使用请求/应答的通信模式：客户端发送一个请求，服务器返回一个响应。

```
Client              Server     
  |                  | 
  |   CON [0xbc90]   | 
  | GET /temperature | 
  |   (Token 0x71)   | 
  +----------------->| 
  |                  | 
  |   ACK [0xbc90]   | 
  |   2.05 Content   | 
  |   (Token 0x71)   | 
  |     "22.5 C"     | 
  |<-----------------+ 
  |                  |
```

CoAP的消息格式如下

![COAP消息](/images/wp/nb_coap_msg.png)

上图中0xFF之前为CoAP的头部(对应HTTP的头部)，中0xFF之后的Payload是要上传的数据（对应的HTTP的Body）。CoAP也使用HTTP REST风格的接口，预定义了4种请求：

- GET方法——用于获得某资源
- POST方法——用于创建某资源
- PUT方法——用于更新某资源
- DELETE方法——用于删除某资源
CoAP的细节请阅读标准文档(https://tools.ietf.org/html/rfc7252),**按照HTTP的思路理解CoAP即可**。

不少NB-IoT模组内置了CoAP协议，这样通过AT指令就可以直接完成CoAP交互了。
