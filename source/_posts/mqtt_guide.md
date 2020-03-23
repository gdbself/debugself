---
title: 物联网IoT协议之mqtt快速入门教程
tags:
  - IoT
  - mqtt
  - OT融合
  - 上云协议
  - 工业4.0
  - 工业互联网
  - 工业通信
  - 物联网
  - 物联网云平台
url: 245.html
id: 245
categories:
  - 物联网IoT
keywords: mqtt,物联网IoT,工业互联网,工业4.0,物联网云平台,上云协议,万物联网
description: '什么是mqtt,物联网IoT上云协议之mqtt零基础快速入门教程'
date: 2019-10-23 23:53:00
---

关注我，获取更多**物联网IoT+云原生**的技术分享。

![](/images/wx.jpg)

[八大物联网IoT上云协议快速入门教程](/2020/03/23/Protocol_guide/)
[物联网IoT协议之mqtt快速入门教程](/2019/10/23/mqtt_guide/)
[物联网IoT协议之OPC UA快速入门教程](/2019/11/27/opc_guide/)
[物联网IoT协议之 LoRaWAN快速入门教程](/2019/12/08/lorawan_guide/)
[物联网IoT协议之NB-IoT/CoAP快速入门教程](/2019/12/18/nb_iot_guide/)
[物联网IoT上云协议之Modbus快速入门教程](/2020/01/01/modbus_guide/)
[IPv6快速入门教程](/2020/02/28/IPv6_guide/)
[物联网IoT协议之6LoWPAN快速入门教程](/2020/03/08/6LoWPAN_guide/)
[物联网IoT协议之HTTP快速入门教程](/2020/03/15/HTTP_guide/)

# 为什么会写这篇mqtt教程

工作中经常用到各种开源的软件，比如docker，k8s，mongo，kafka等，也经常网上搜集各种学习资料，但是都没付过费，都是免费开源的，真要感谢丰富的开源世界，不然工作都没法进行了。时常想，老是白嫖可不行，也得分享点东西出来。最近工作中一直在用mqtt，于是准备写份mqtt教程，把学习到的mqtt知识分享出来。为了方便不同的读者，教程分为两大部分：
- mqtt快速入门教程，快速了解如何使用mqtt，无须需要关心mqtt底层细节
- mqtt完整教程，包括mqtt各种技术细节，适合mqtt进阶

源码先生 写于2019-10-19

---

# 物联网IoT协议之mqtt快速入门

## 什么是mqtt
 mqtt是IBM 公司在1999年开发的即时通讯协议，它是一个轻量级的，基于发布/订阅模式的消息传输协议，专门为资源（如内存，网络等）受限的设备而设计，即使设备运行在低带宽、高延时、不稳定的网络中，通过mqtt依旧可以获得一定程度的、可靠的通信服务。
 
## 为什么物联网IoT需要mqtt
- 物联网的首要任务是完成设备与设备的通信，而且不仅是一对一的通信，还包括多对多的通信，mqtt基于发布/订阅模式，原生支持多对多的通信，非常符合物联网的通信要求
- mqtt是轻量级协议，相比HTTP，mqtt协议层占用的通信数据非常少，非常适合低带宽、资源受限的IoT设备
- 无论是国外的AWS、Azure，还是国内的阿里云、腾讯云，都使用mqtt作为接入IoT设备的首选协议，mqtt已经成为物联网上云第一大协议

## mqtt之Hello World
按照惯例，如果不懂mqtt，来个Hello World想必是极好的。mqtt是即时通信协议，和QQ/微信/手机短信非常像，这里就以小明和丽丽互发短信为例，作为mqtt的Hello World。开始Hello World前，需要先了解mqtt中的几个名词。

### 消息主题(topic)
可以类比为手机号，发短信前得先知道目标手机号，mqtt中发消息前得先知道目标topic。
topic是一个字符串，比如xiaoming，mqtt中常使用/作为分隔符，如/lanxiang/wajueji/xiaoming这样的格式，可以方便的看出这是蓝翔-挖掘机班-小明同学的topic。

### 消息发布(publish)
publish用来发布一条消息，伪代码为

```
publish('topic', 'payload')
```

其中topic为要发布消息的主题，payload为要发布消息的内容。

### 消息订阅(subscribe)
subscribe用来订阅一个消息主题，伪代码为

```
subscribe("topic")
```

其中topic为要订阅的消息的主题，订阅成功后，应用程序还需要注册一个回调函数，当有消息发送过来，回调函数会被调用，伪代码如

```
client.on("message", function (topic, payload) {
    alert([topic, payload].join(": "))
  })
```

其中on("message")为监控消息达到事件，function为回调函数，topic为收到的消息来自哪个主题，payload为收到消息的内容。

### mqtt服务器(broker)
设备通过发布/订阅模式通信，设备需要先连接到mqtt服务器，多个设备之间的消息需要通过mqtt服务器中转，如下图
![](/images/wp/mqtt_broker.png)
*发布方(publisher)即发送消息的设备*<br>
*订阅方(subscriber)即订阅消息的设备*<br>
mqtt服务器类似消息的中转站，所以mqtt服务器有一个更形象的名词，即消息代理服务器(Broker)。

### 客户端(client)
发布方和订阅方统称为客户端(client)，一个设备可以即是发布方，也可以同时是订阅方，一个设备可以向多个topic发布消息，也可以订阅多个topic。

## 实现Hello World
下面我们完成小明和丽丽互发短信的示例。
### 选择一个在线mqtt服务器
首先我们要有一个mqtt服务器，由于mqtt只是一份标准规范，而目前符合mqtt标准的服务器已经有很多了，开源的有mosquitto，RabbitMQ，EMQ X Broker等。自己手动安装服务器，很容易出现选择困难症，到底哪家mqtt服务器强？其实最简单的方法，是直接使用网上公开的，在线mqtt服务器，mqtt官方公布了一个在线mqtt服务器列表，见[在线mqtt服务器](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers)。这些线上服务器仅做测试使用，这里我们选取test.mosquitto.org作为Hello World的测试服务器。

### 选择一个mqtt客户端
mqtt客户端也有很多选择，经过源码先生的验证，开源的[qmqtt-client](https://github.com/emqx/qmqtt-client)使用比较简单，源码先生已经把qmqtt-client编译为exe，请直接从百度网盘<https://pan.baidu.com/s/1ZTmmuSddLzRCoW4xLVCdvw> 下载吧。
双击qmqtt-client.exe，即可启动mqtt客户端。

### 小明和丽丽互发短信
启动一个mqtt客户端，代表小明，按照下图中的步骤连接mqtt服务器。
![](/images/wp/mqtt_xiaoming_con.png)
启动另一个mqtt客户端，代表丽丽，按照下图中的步骤连接mqtt服务器。
![](/images/wp/mqtt_lili_con.png)
小明订阅消息主题/lanxiang/wajueji/xiaoming，类似小明给自己买了个手机号。
![](/images/wp/mqtt_xiaoming_sub.png)
丽丽订阅消息主题/lanxiang/meirong/lili，类似丽丽给自己买了个手机号。
![](/images/wp/mqtt_lili_sub.png)
*注意，不需要手动创建topic，第一次订阅或者第一次发布时服务器会自动创建topic*<br><br>
小明要给丽丽发信息，必须先知道丽丽的手机号，也不知道小明从哪里弄来了丽丽的手机号（/lanxiang/meirong/lili），总之，小明可以给丽丽发信息了。发信息的步骤如下图：
![](/images/wp/mqtt_xiaoming_pub.png)
丽丽收到了新的消息，并回复了小明。
![](/images/wp/mqtt_lili_pub.png)
小明也收到了丽丽的回复。
![](/images/wp/mqtt_xiaoming_recv.png)
小明心情不太好，此次mqtt通信到此结束。

### linux版mqtt客户端
linux版mqtt客户端推荐mosquitto-clients，Ubuntu安装的mosquitto-clients的命令为

```
apt-add-repository ppa:mosquitto-dev/mosquitto-ppa
apt-get update
apt-get install mosquitto-clients
```

小明订阅消息的命令为：

```
mosquitto_sub -v -t '/lanxiang/wajueji/xiaoming' -h "test.mosquitto.org" -p 1883
```
丽丽订阅消息的命令为：

```
mosquitto_sub -v -t '/lanxiang/meirong/lili' -h "test.mosquitto.org" -p 1883
```
小明发布消息的命令为:

```
mosquitto_pub -h 'test.mosquitto.org' -p 1883 -t '/lanxiang/meirong/lili' -m '是丽丽吗，我是小明，在干嘛呢？'
```
丽丽发布消息的命令为:

```
mosquitto_pub -h 'test.mosquitto.org' -p 1883 -t '/lanxiang/wajueji/xiaoming' -m '准备睡了，有空再聊'
```

## 开发mqtt应用程序
开发mqtt的应用程序，需要使用mqtt客户端库，mqtt客户端库会提供publish、subscribe等API，应用程序调用客户端库的API即可完成mqtt通信。mqtt官方提供一个客户端库列表，见[mqtt客户端库列表](https://github.com/mqtt/mqtt.github.io/wiki/libraries)，其中Java，JavaScript，Python，go各种语言的mqtt客户端库应有尽有，请根据自己的口味自行选取。
### C语言mqtt客户端示例
IoT设备端一般使用C语言开发，这里以[Eclipse Paho Embedded C](https://github.com/eclipse/paho.mqtt.embedded-c)客户端库为例，发布方的示例代码为：

```
#include <stdio.h>
#include <memory.h>
#include "MQTTClient.h"

int main(int argc, char** argv)
{
	int rc = 0;
	unsigned char buf[100];
	unsigned char readbuf[100];

	Network n;
	MQTTClient c;

	NetworkInit(&n);
	NetworkConnect(&n, "test.mosquitto.org", 1883);
	MQTTClientInit(&c, &n, 1000, buf, 100, readbuf, 100);
 
	MQTTPacket_connectData data = MQTTPacket_connectData_initializer;       
	data.willFlag = 0;
	data.MQTTVersion = 3;
	data.clientID.cstring = "xiaoming";

	data.keepAliveInterval = 10;
	data.cleansession = 1;
	
	// 连接mqtt服务器
	rc = MQTTConnect(&c, &data);
	printf("Connected %d\n", rc);
    
	// 发布消息
	MQTTMessage pubmsg;
	memset(&pubmsg, '\0', sizeof(pubmsg));
	pubmsg.payload = "是丽丽吗";
	pubmsg.payloadlen = strlen(pubmsg.payload);
	pubmsg.qos = 0;
	pubmsg.retained = 0;
	pubmsg.dup = 0;
	
	rc = MQTTPublish(&c, "/lanxiang/meirong/lili", &pubmsg);
	printf("Published %d\n", rc);

	MQTTDisconnect(&c);
	NetworkDisconnect(&n);
	return 0;
}
```
订阅方的示例代码为：

```
#include <stdio.h>
#include <memory.h>
#include "MQTTClient.h"

#include <signal.h>

//通过信号退出程序
volatile int toStop = 0;
void cfinish(int sig)
{
	signal(SIGINT, NULL);
	toStop = 1;
}

//新消息到达的回调函数
void messageArrived(MessageData* md)
{
	MQTTMessage* message = md->message;

	printf("%.*s\t", md->topicName->lenstring.len, md->topicName->lenstring.data);
	printf("%.*s\n", (int)message->payloadlen, (char*)message->payload);
}

int main(int argc, char** argv)
{
	int rc = 0;
	unsigned char buf[100];
	unsigned char readbuf[100];

	Network n;
	MQTTClient c;

	signal(SIGINT, cfinish);
	signal(SIGTERM, cfinish);

	NetworkInit(&n);
	NetworkConnect(&n, "test.mosquitto.org", 1883);
	MQTTClientInit(&c, &n, 1000, buf, 100, readbuf, 100);
 
	MQTTPacket_connectData data = MQTTPacket_connectData_initializer;       
	data.willFlag = 0;
	data.MQTTVersion = 3;
	data.clientID.cstring = "lili";
	data.keepAliveInterval = 10;
	data.cleansession = 1;
	
	// 连接mqtt服务器
	rc = MQTTConnect(&c, &data);
	printf("Connected %d\n", rc);
    
	// 订阅mqtt消息
	rc = MQTTSubscribe(&c, "/lanxiang/meirong/lili", 0, messageArrived);
	printf("Subscribed %d\n", rc);

	while (!toStop)
	{
		MQTTYield(&c, 1000);
	}
	
	printf("Stopping\n");

	MQTTDisconnect(&c);
	NetworkDisconnect(&n);

	return 0;
}
```

### JavaScript语言mqtt客户端示例
使用npm命令安装JavaScript的mqtt客户端库。

```
npm install mqtt
```

发布方的示例代码为：

```
var mqtt = require('mqtt')
var client = mqtt.connect('mqtt://test.mosquitto.org')

client.on('connect', function () {
  client.publish('/lanxiang/meirong/lili', '是丽丽吗')
  client.end()
})
```
订阅方的示例代码为：
```
var mqtt = require('mqtt')
var client = mqtt.connect('mqtt://test.mosquitto.org')

client.on('connect', function () {
  client.subscribe('/lanxiang/meirong/lili', function (err) {
    if (err) {
        console.log(err);
    }
    else{
        console.log("subscribe succeed!");
    }
  })
})

client.on('message', function (topic, message) {
  console.log(message.toString())
  client.publish('/lanxiang/wajueji/xiaoming', '准备睡了，有空再聊')
  client.end()
})
```
### 云厂商mqtt接入说明
AWS，阿里云等云厂商，都提供了物联网设备接入的云平台，虽然它们使用的也是mqtt，但是使用上述标准的mqtt客户端库是无法接入这些物联网云平台的，必须使用云厂商自己的mqtt SDK才能接入，这是因为此类物联网云平台添加了特有的功能（而标准的mqtt客户端库并没有这些特有的功能），主要包括
- 特有的安全认证，mqtt默认只有用户名和密码的认证，而云厂商提供了自己特有的认证方式，其安全性会更好
- 特有的业务逻辑，云厂商一般需要先通过Web控制台创建设备信息(包括设备所属产品，设备ID，设备密钥等)，再把设备信息烧写到设备固件中，然后才可以连接到云平台

云厂商一般都会提供详细的设备接入文档和接入SDK，可以去[阿里云mqtt SDK](https://help.aliyun.com/document_detail/42648.html?spm=a2c4g.11186623.6.568.16687544S9dk7e)自行围观阿里的mqtt客户端库。

# 物联网IoT协议之mqtt完全教程
敬请期待。
