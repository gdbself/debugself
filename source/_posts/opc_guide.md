---
title: 物联网IoT协议之OPC UA快速入门教程
tags:
  - IoT
  - mqtt
  - OPC
  - OPC UA
  - OT融合
  - 上云
  - 上云协议
  - 工业4.0
  - 工业互联网
  - 工业通信
  - 物联网
  - 物联网云平台
url: 262.html
id: 262
categories:
  - IoT
keywords: OPC UA,物联网IoT,工业互联网,工业4.0,物联网云平台,上云协议
description: '物联网IoT协议之OPC UA零基础快速入门教程 工业互联网 工业4.0 上云协议 物联网云平台 万物联网 mqtt'
date: 2019-11-27 13:00:54
---

关注我，获取更多物**联网IoT+云原生**的技术分享。 
![源码先生微信公众号](/images/wx.jpg) [物联网IoT协议之mqtt快速入门教程](https://www.debugself.com/archives/245)

## 什么是OPC UA

OPC UA是一套通信协议标准，完成设备与设备，设备与应用之间的数据交互。源码先生的[物联网IoT协议之mqtt快速入门教程](https://www.debugself.com/archives/245)中的mqtt也是一套数据交互的协议标准，两者虽然协议内容不同，但最终目的一致，都是为了实现不同系统的数据交互。 OPC UA前身是OPC，第一个OPC规范在1996年发布，包括一整套接口、属性和方法的标准集；OPC基于Windows操作系统的COM技术，所以只能运行在Windows上。 OPC UA是OPC的重大升级，在平台独立性、安全性，可靠性等方面做出了升级，更能适应现代工业通信的需求，虽然升级内容很多，但OPC UA 对比OPC并无革命性的变化。

## OPC UA用在什么场景

OPC主要用在自动化控制系统、仪器仪表及过程控制系统，具有很明显的工业基因，而且OPC发展这么多年，也很少扩展到非工业领域。 随着近年物联网、工业4.0、工业互联网、IT和OT(Operational Technology)融合等需求的出现，人们希望把工业设备融入到物联网中，深耕工业领域的OPC UA，自然成为大家关注的重点对象！

## 通过对比mqtt入门OPC UA

mqtt和OPC UA都是为了实现不同系统的数据交互，mqtt相对比较简单，mqtt通过发布订阅实现数据交互，而OPC UA要复杂不少，通过对比两者的差异，对入门OPC UA很有帮助。

### 拓扑结构的对比

在mqtt中，设备以及应用程序都作为客户端连接到mqtt服务器(Broker)，形成以mqtt服务器为中心的拓扑结构。 ![mqtt拓扑结构](/images/wp/opc_ua_mqtt_topo.png) 在OPC UA中，**设备不是作为客户端，而是作为OPC UA Server提供各种服务，应用程序作为客户端连接到设备，调用OPC UA Server提供的各种服务**。 ![](/images/wp/opc_ua_topo1.png) OPC UA中，**设备除了作为OPC UA Server，也可以同时作为客户端连接到其他设备，形成非中心化的拓扑结构**。 ![](/images/wp/opc_ua_topo2.png)

### 业务模型对比

mqtt标准中没有定义业务模型，mqtt服务器只是个中转站，用来透传转发多个客户端的消息。应用程序需要在应用层自定义业务模型，主流物联网云平台都使用类似json的格式描述业务模型，阿里云的物模型如下图 ![](/images/wp/opc_ua_ali.png) OPC UA中，**使用面向对象的方式定义业务模型**。面向对象中通过类(包括类成员变量，类成员函数，类实例)，以及类之间的继承关系实现对业务的建模，而OPC UA的**通过节点，以及节点之间的引用关系实现对业务的建模**。 ![](/images/wp/opc_ua_nodes.png)

#### OPC UA的节点(Node)

节点是OPC UA数据交互的基本单元，**客户端和服务器之间的交互通信，都是基于节点进行的**。OPC UA中预定义了8种节点：

* ObjectNode
* ObjectTypeNode
* VariableNode
* VariableTypeNode
* MethodNode
* ReferenceTypeNode
* DataTypeNode
* ViewNode

节点的属性用来描述节点，下面是所有节点共有的通用属性。 ![](/images/wp/opc_ua_attrs.png)

#### OPC UA的引用(Reference)

引用描述了两个节点之间的关系，**用来把多个节点关联起来**，OPC UA预定义了多种引用，下面重点介绍几种常用的引用。

##### hasTypeDefinition

hasTypeDefinition用来描述面向对象**中类、类实例之间的关系**。

* ObjectNode的hasTypeDefinition引用，指向了一个ObjectTypeNode，表示该ObjectNode的类型;
* VariableNode的hasTypeDefinition引用，指向一个VariableTypeNode，表示该 VariableNode的类型。

##### hasSubType

hasSubType用来描述面向对象中的**继承关系**。当子类从父类继承后，子类拥有一个hasSubType引用指向父类。

##### hasComponents

hasComponents类似设计模式中的**组合模式**。

* ObjectNode一般都由多个VariableNode组成，ObjectNode包含某个VariableNode时，ObjectNode拥有一个hasComponents引用，指向该VariableNode；
* VariableNode也可以包含子VariableNode，此时也用hasComponents描述它们的关系。

##### Organizes

Organizes用来指明两个节点的层次结构，可以类比为**文件夹和文件的关系**，通过Organizes，可以把多个文件(节点)组织到同一个文件夹(父节点)下面。 完整的引用类图如下。 ![](/images/wp/opc_ua_refs.png)

#### OPC UA中的服务(Service)

服务可以看做是OPC UA Server提供的API集合，也可以看做是RPC(Remote Procedure Call)接口集合，服务以方法的方式提供出来，服务使用了类似Web的请求-应答机制，当客户端调用一个服务时，客户端发送一个请求给服务器，服务器处理完成请求后，返回响应消息给客户端。 **客户端访问服务器提供的所有功能，都是通过调用服务完成。** OPC UA预定义了37个标准服务，按功能分类如下图 ![](/images/wp/opc_ua_services.png) 其中常用的服务有：

* 通过读写服务，可以获取服务器上指定节点指定属性的值；
* 调用服务，可以执行服务器上执行节点的方法；
*   订阅数据变化和时间，可以监控服务器数据的变化。

### 语义化对比

mqtt标准中没有定义语义化的内容，**应用程序要想知道设备提供了哪些功能/服务，只能靠应用程序和设备双方提前约定，或者通过阅读设备提供的说明文档**。 OPC UA Server中节点的通用属性BrowseName、DisplayName、Description等描述了节点的属性和功能，**应用程序连接到OPC UA Server后，通过浏览这些属性就可以了解设备提供的功能和服务**。

### 发布订阅对比

mqtt和OPC UA都提供了发布订阅的功能，**OPC UA的发布订阅功能用来实现设备数据的监控**，但设备数据有变化时，订阅者会实时收到通知。

## OPC UA之Hello World

上面的概念很抽象，要先对OPC UA有初步的认识，还是来个Hello World比较靠谱。

### 选择OPC UA Client

免费且好用的OPC UA客户端，源码先生推荐Unified Automation公司的UAExpert，在其官网https://www.unified-automation.com/上注册后可以免费下载，也可以到百度云盘下载 https://pan.baidu.com/s/1TM1yQ-K1bUBDav_rABb75Q 下载，提取码：11f8 。

### 选择"在线OPC UA Server"

相比mqtt官方提供了一大票公开的mqtt测试服务器地址，公开的在线OPC UA Server少的可怜，源码先生在互联网上认真了搜了一番，只能找到唯一的一个，即opc.tcp://opcua.rocks:4840/ 。

#### UAExpert连接"在线OPC UA Server"

启动UAExpert，依次选择\[Server\]--\[Add\]--\[Advanced\]，按下图输入信息。 ![](/images/wp/opc_ua_online_add.png) 通过右键菜单连接服务器。 ![](/images/wp/opc_ua_online_connect.png) 第一次连接时，会提示服务器证书的警告，此时需要选择信任(Trust)服务器的证书。 ![](/images/wp/opc_ua_online_trust.png) 连接成功后，会在地址空间中显示出服务器的全部节点。 ![](/images/wp/opc_ua_online_connected.png) 展开节点后，会发现opc.tcp://opcua.rocks:4840/提供了一个Hello World的方法，可通过右键菜单调用该方法。 ![](/images/wp/opc_ua_online_call.png) Hello World方法需要输入参数，这里填入测试字符串“www.debugself.com” ，并点击Call按钮。 ![](/images/wp/opc_ua_online_call_input.png) 服务器执行Hello World方法，返回执行结果。通过输出参数可以看出，Hello World方法，简单的把输入参数前面添加了Hello后，然后返回给客户端的。 ![](/images/wp/opc_ua_online_call_output.png)

### 部署本地OPC UA Server

上面的“在线OPC UA Server”网速很慢，测试时可能连接失败，这是也可以自己部署本地OPC UA Server。https://github.com/Pro/opcua-animal-server 是一个使用open62541构建OPC UA Server的示例，为了方便部署，我把opcua-animal-server构建成docker镜像(构建过程请参考 https://github.com/gdbself/opcua-animal-server/ )，方便一键本地部署。 以Ubuntu系统为例，首先要安装docker（docker已经是云端开发的基础设施，不了解docker的话，有必要借此学习下docker的用法）。
```
apt-get install docker
```    

运行下面的docker命令部署本地OPC UA Server（真的一键部署，很方便，有木有？）。
```
docker run -d -p 4840:4840 debugself/opcua-animal-server:20191125
```    

#### UAExpert连接“本地OPC UA Server”

启动UAExpert，依次选择\[Server\]--\[Add\]--\[Advanced\]，按下图输入信息。 ![](/images/wp/opc_ua_local_add.png) **_注意_，请把图中的192.168.170.128替换为自己Ubuntu电脑的IP地址。** 连接OPC UA Server成功后，可以看到服务器地址空间中显示了所有节点。选中Cat的Name节点，在右侧的属性框中可以看到这只猫的Value属性值为Cattie，意为这是猫的名称为Cattie。 ![](/images/wp/opc_ua_local_look.png)

## OPC UA的学习资料

* 学习OPC UA的细节，源码先生推进阅读 https://open62541.org/doc/current/index.html ,比官方的文档简单明了；
* C语言实现的开源OPC UA：https://github.com/open62541；
* 官方OPC UA标准文档：https://reference.opcfoundation.org/v104；
* 官方提供的OPC UA实现：https://github.com/OPCFoundation ，包括C、C++、C#、Java语言实现的OPC UA Server和Client。

OPC UA中的概念很多，初学者会感觉OPC UA很复杂，这里再强调一次，OPC UA本质是按照面向对象的方式对业务建模，搞不懂OPC UA时，按照面向对象的思路，基本可以做到拨云见日。
