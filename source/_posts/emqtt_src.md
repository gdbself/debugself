---
title: emqtt源码简单分析
tags:
  - emqtt
  - Erlang
  - IoT
  - mqtt
  - 物联网
url: 212.html
id: 212
categories:
  - IoT
date: 2018-05-18 17:05:35
---

**emqtt源码分析**
-------------

**gen_server2.erl**:客户端/服务器模型，服务器是一个独立的进程，该进程是一个消息循环，客户端可以向服务器发送消息，服务器通过消息循环来处理消息

emqttd\_client.erl使用了gen\_server2.erl客户端/服务器模型，emqttd\_client:enter\_loop为进入服务器的消息循环

emqttd_session.erl使用了gen\_server2.erl客户端/服务器模型，emqttd\_session:start_link为启动服务器（并进入的消息循环)

emqttd\_ws\_client.erl、emqttd\_cm.erl、emqttd\_sm.erl、emqttd\_pubsub.erl等都使用了gen\_server2.erl客户端/服务器模型

**emqttd_client.erl**:代表一个客户端连接

每当一个新的mqtt客户端连接到mqtt服务器后，就新增一个emqttd_client;

emqttd\_app.erl:start\_listener中，esockd:open监控到新的客户端连接后，调用emqttd\_client:start\_link新增一个emqttd_client

客户端发送的数据，在emqttd_client:received中被处理，被转发给emqttd_protocol:receive处理

**emqttd_cm.erl**:即emqttd client manager，管理所有emqttd_client

新增emqttd_client时，把client信息保存在ets表中

emqttd_client断开时，把client信息从ets表中删除

**emqttd_protocol.erl**：客户端发送的数据，被转发到这个文件中处理，见emqttd_protocol:process

emqttd_protocol：subscribe，处理客户端订阅主题,又被转发给emqttd_session:subscribe处理

emqttd_protocol：publist，处理客户端发布主题,又被转发给emqttd_session:subscribe处理

**emqttd_session.erl**：用来处理Client和Server的交互

保存了服务器发送给客户端，但是没有收到PUBACK的QoS1和QoS2消息

保存了因为客户端不在线，pending发送的消息

一个emqttd\_client对应对一个emqttd\_session，每一个emqttd\_client都会新建一个MQueue，也会新建一个emqtt\_inflight；MQueue和emqtt_inflight都是用来保存上述QoS1和QoS2消息的（见emqttd_mqueue.erl文件开头的注释）

**emqttd_sm.erl**:即emqttd session manager，管理所有emqttd_session

新增emqttd_session时，把session信息保存在ets表中

emqttd_session断开时，把session信息从ets表中删除

**emqttd_server.erl**：emqttd\_session:subscribe被转发到这里，又被转发到emqttd\_pubsub

**emqttd_pubsub.erl**：最终处理subscribe和publish的地方

  

**emqttd_inflight.erl**：封装了erlang原生的gb\_trees，主要用在emqttd\_session中保存消息（emqttd_mqueue.erl文件中开头有说保存消息的意义）

**emqttd_mqueue.erl**:封装了erlang原生的queue，主要用在emqttd_session中保存消息

**emqttd_metrics.erl**:完成mqtt服务器数据统计，如统计接收到多少个packet，发送多少个packet

* * *

**底层通信socket使用的是https://github.com/emqtt/esockd，它是异步**

**从socket异步接收数据的流程**

esockd\_connection:async\_recv -->

esockd\_transport:async\_recv -->

prim\_inet:async\_recv

而prim\_inet是一个更底层的函数，他是异步的，调用async\_accept的话，进程会在accept socket后收到消息{inet_async，…}，prim\_inet:async\_recv同理

[http://blog.csdn.net/yuanlin2008/article/details/8277404](http://blog.csdn.net/yuanlin2008/article/details/8277404)  

[http://blog.csdn.net/aaaajw/article/details/51725244](http://blog.csdn.net/aaaajw/article/details/51725244)  

**处理发布订阅的流程**
-------------

emqttd_protocol:received -->  

emqttd_protocol:process -->

process根据数据包的类型（如CONNECT\_PACKET、PUBLISH\_PACKET、SUBSCRIBE_PACKET）做出不同的处理-->

以 emqttd_protocol:publish为例 -->

若QOS>0会先调用with\_puback用来返回PUBACK --> 若QOS=0，调用emqttd\_session:publish -> emqttd_server:publish ->

emqttd_pubsub:publish-->

emqttd_pubsub:route路由消息

**subscribe **

emqttd\_client:subscribe --> emqttd\_protocol:subscribe  --> emqttd_session:subscribe 

emqttd\_protocol:check\_acl     判断是否有权限publish

  

publish发布的消息内容结构#mqtt_message，

delivered to client(::ffff:172.18.0.10/mqttjs\_4c414d7b): Message(Q1, R0, D0, MsgId=<<0,5,106,166,191,75,162,100,111,171,0,0,55,189,0,1>>, PktId=3, From=xx/mqttjs\_c968e2ed, Topic=/d2s/candtu/12344321/online)  
其中

MsgId是emqtt自动产生的guid：msgid() -> emqttd_guid:gen()

PktId是客户端（即发布publish的客户端）自动产生的id：MQTT.js --> client.js --> messageId:this._nextId()

**keepalive流程**  

------------------

emqttd\_protocol:process新建emqttd\_session时--> start\_keepalive发送keepalive消息--> emqttd\_client:handle\_info({keepalive,start接收并处理消息--> emqttd\_keepalive:start(StatFun读取连接状态，启动定时器-->定时器超时后回调emqttd\_client:handle\_info({keepalive,check，如果keepalive超时，shutdown连接

**网络部稳定的处理/相同clientid的处理**  

-----------------------------

mqtt服务器根据clientid识别mqtt客户端，只要mqtt客户端clientid，就认为是同一个客户端；

该功能用来处理网络不稳定的情况，比如mqtt客户端client\_my\_id连接服务器后，网络不稳定，客户端认为自己的连接已经断开了，但是服务器由于必须等待keepalive超时才能确定客户端连接断开，所以服务器依旧保持者客户端的连接；然而网络变好后，客户端使用新的socket连接到服务器，这时候客户端发送的clientid必须还是client\_my\_id，服务器会明白这是同一个客户端，会删除旧连接，使用新连接，流程如下：

emqttd\_protocol:process新建emqttd\_session时--> emqttd\_sm:start\_session-->查询clientid，如果已经存在，resume\_session,踢掉旧连接，更新emqttd\_sm使用新的连接；如果clientid不存在，新建emqttd_session