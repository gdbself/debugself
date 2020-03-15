---
title: 物联网IoT协议之HTTP快速入门教程
tags:
  - IoT
  - HTTP
  - OT融合
  - 上云协议
  - 工业4.0
  - 工业互联网
  - 工业通信
  - 物联网
  - 物联网云平台
categories:
  - 物联网IoT
keywords: IPv6,IPv4,HTTP1.1,HTTP 2.0,HTTP 3.3,UDP,TCP,SPDY,QUIC,物联网IoT,工业互联网,工业4.0,物联网云平台,上云协议,万物联网
description: '什么是HTTP,HTTP简介,物联网IoT上云协议之HTTP零基础快速入门教程,HTTP在物联网的应用'
date: 2020-03-15 14:37:00
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

# 物联网IoT协议之HTTP快速入门教程

HTTP并不是为物联网设计的，但是HTTP太流行了，在没有智能手机的年代，谁的上网不是从浏览器里输入 http://www.xxx.com 开始的？即使现在手机APP不需要手动输入网址了，APP后台也少不了HTTP通信。物联网的COAP协议和HTTP非常类似，且更能满足物联网低功耗等特点，但**物联网依旧离不开HTTP，HTTP的市场占有率决定了，谁支持HTTP，谁就能快速接入丰富多彩的互联网世界**.

## HTTP
HTTP(HyperText Transfer Protocol)，即超文本传输协议，从字面上看，涉及两个问题:
### 在谁和谁之间传输
在客户端和服务器之间传输。

![HTTP的客户端/服务器模型](/images/wp/HTTP_cs.png)

客户端最常见的是浏览器，服务器即Web服务器，常见的有Apache，Nginx等。

### 传输的是什么内容
各种资源，如文字，图片，视频，音频等等，本质都是二进制数据。

## HTTP是基于TCP/IP的应用层协议
OSI标准7层网络模型中，HTTP是最上层的应用层协议，HTTP下面是TCP传输层。

![HTTP OSI 7层网络模型](/images/wp/HTTP_osi.png)

### HTTP基于TCP到底是什么意思
以查询手机归属地作为示例，浏览器访问 http://mobsec-dianhua.baidu.com/dianhua_api/open/location?tel=13843859438 可以看到归属地查询结果，这里通过使用命令行分步骤实现归属地的查询，看看HTTP基于TCP的细节。

![nc命令模拟HTTP请求](/images/wp/HTTP_nc.png)

1. 基于TCP，需要首先建立TCP连接，linux上使用nc命令可以建立TCP连接
```
nc -v mobsec-dianhua.baidu.com 80
```
上述命令，会自动获取mobsec-dianhua.baidu.com对应的IP地址180.101.212.32，然后建立到180.101.212.32地址80端口的TCP连接。

2. TCP连接成功后，可以看到如下字符串
```
Connection to mobsec-dianhua.baidu.com 80 port [tcp/http] succeeded!
```

3. 基于TCP连接，发送数据，数据内容为HTTP格式的字符串
```
GET /dianhua_api/open/location?tel=13843859438 HTTP/1.1
Host: mobsec-dianhua.baidu.com
Accept: */*

```
上述内容全部是字符串，但是字符串是符合HTTP标准的。

4. 服务器返回数据，TCP收到数据，数据内容为HTTP格式的字符串
```
HTTP/1.1 200 OK
Accept-Ranges: bytes
Cache-Control: max-age=60
Content-Length: 229
Content-Type: application/json; charset=UTF-8
Date: Sat, 14 Mar 2020 16:28:20 GMT
Etag: W/"79ef578d5af7ed1b85c24721c997e572"
Last-Modified: Sat, 14 Mar 2020 16:28:20 GMT
Server: nginx
Vary: Accept-Charset, Accept-Encoding, Accept-Language, Accept

{"response":{"13843859438":{"detail":{"area":[{"city":"松原"}],"province":"吉林","type":"domestic","operator":"移动"},"location":"吉林松原移动"}},"responseHeader":{"status":200,"time":1584203300075,"version":"1.1.0"}}
```

上面的示例可以看出，HTTP的内容是ASCII码字符串，而不是二进制原始数据，把HTTP字符串通过TCP连接发送出去，即实现了HTTP请求，当TCP连接返回HTTP字符串时，即收到了HTTP应答。

### HTTP的请求/应答模式

![HTTP 请求/应答模式](/images/wp/HTTP_req_res.jpg)

客户端发送HTTP请求，服务器返回HTTP应答。在HTTP早期版本，一个TCP连接，只能完成一次请求+应答，完成后TCP连接被断开；如果有新的请求，必须再建立新的连接，这称为**短连接**。随着HTTP版本的迭代更新，开始支持**长连接**，一次请求+应答完成后，TCP连接可以不断开，继续用来发送下一个的请求+应答。

## HTTP报文格式
HTTP报文内容是ASCII字符串，分为三大部分：

![HTTP 报文格式](/images/wp/HTTP_packet.png)

起始行和首部是各种配置参数，而**主体是资源本身，如图片，文字等**；

![HTTP 报文说明](/images/wp/HTTP_packet2.png)

报文又可以分为请求报文和应答报文，两者的格式基本是一致的，但是细节有区别。报文示例如下

![HTTP 报文示例](/images/wp/HTTP_packet_demo.png)

下面重点讲述下HTTP报文中常用内容。

### HTTP请求报文--起始行
请求报文中的起始行主要**包括方法Method、版本号、URL三部分**。

#### 请求报文--起始行--方法Method
方法用来区分对资源文件的各种操作，常用的方法包括：增删改查。

方法 | 作用
---|---
POST | 向服务器端发送数据,服务器一般会保存数据
DELETE | 删除资源文件
PUT | 更新资源文件
GET | 获取资源文件

**上述增删改查四种方法只是通用约定，没有强制要求**，POST和PUT都是向服务器上传资源，两者没有明显的界限，出现混用也是没问题的，甚至GET也可以用来上传资源，GET从服务器获取资源时，一般会附带些参数，如果服务器把GET参数保存下来，相当于使用GET上传资源了。

#### 请求报文--起始行--协议版本号
常用的版本号有HTTP/1.0、HTTP/1.1、HTTP/2.0等，不同版本支持的功能也有不同，一般服务器会支持多个版本的HTTP，客户端发送请求时，在起始行中填写客户端想要使用的HTTP版本，**这是个协商过程，双方达成一致，然后使用同一个HTTP版本通信即可**。

#### 请求报文--起始行--URL
Uniform Resource Locator，统一资源定位符，用来定位服务器上唯一的资源，以便客户端访问该资源。URL由4部分组成：协议、主机、端口、路径，URL的一般语法格式为：

```
protocol :// hostname[:port] / path /subpath/ [;parameters][?query]#fragment
```
其中，方括号[]为可选项，

- protocol：定义服务的协议类型，最常见的类型是http、https
- hostname： 域名/主机名，如 baidu.com ，域名是分级的，又可以分为 www.baidu.com 、 mp3.baidu.com 等，因为www很常见，造成大家误认为www是必须的，其实不然，**就是改成 www250.baidu.com 也是可以的**，只要DNS能正确解析域名，返回最终的IP地址， www250.baidu.com 也是可以正常访问的。
- port：http默认端口号是 80，https默认是443端口，默认端口是可以省略的
- path/subpath：服务器上资源路径，如果省略，则为根目录
- parameters:参数,参数是key=value的形式，**参数前需要用分号";"**
- query：查询字符串，用于查询时附加更详细的参数，**query前需要用问号“?”**，query是键值对，每个键值都是key=value的形式，**多个键值对之间以 & 符号分隔**，如?postid=5038412&t=1450591802326，服务器会根据参数串的 & 和 = 对参数进行解析片段
- #为片段，当网页内容比较长时，浏览器使用片段直接定位到具体的位置。

query是键值对，可以自定义添加各种key=value，所以**URL中是可以承载很多信息的，但是浏览器对URL有长度限制**，URL不能无限长。

### HTTP请求报文--请求头部/首部
HTTP 规范的首部分为几大类：
- 通用首部
- 请求首部
- 响应首部
- 实体首部
- 扩展首部

这些首部有什么区别呢，详细说起来比较复杂，源码先生建议可以先不关心这些首部的区别，**他们的共同点是：所有首部都是Key:Value格式，每个Key:Value占一行，HTTP标准预定义了很多Key，应用程序也可以自定义Key：value**，大家熟悉常用的、预定义的Key的含义和用法即可，下面是常用Key说明。

#### Accept
客户端发送的请求报文中，使用Accept告诉服务器，客户端能够接受的资源类型，服务器会尽量按照Accept的要求返回资源。Accept的示例：

```
Accept: image/jpeg,application/json,*/*
```
image/jpeg表示客户端可以接受jpeg的图片，"*/*"表示客户端可以接受任何类型的资源。

#### Content-Type
主体的资源类型，这里的资源类型和Accept的资源类型含义相同，常见的资源类型有
- text/plain
- text/html
- text/css
- image/jpeg
- image/png
- image/svg+xml
- audio/mp4
- video/mp4
- application/javascript
- application/json
- application/pdf
- application/zip
- application/atom+xml

资源的含义，按照字面意思理解即可，都是常见的后缀。

##### Content-Type之application/json
json是key:value组合成的字符串，可读性非常好，不管是互联网和是物联网，json都很常用，比如物联网传感器要上报温度和湿度，使用如下的json

```
{
	"temperature": 25,
	"humidity": 80
}
```
一眼就可以看出上述json的含义，服务器读取key为temperature的值即获取到温度，读取key为humidity的值即获取到湿度。

### Content-Length
主体的长度或size，即资源的大小。

### 其他常见的首部Key
- Content-Encoding：主体资源的编码方式，如ASCII,utf-8,GBK等
- Content-Language：主体资源对应的自然语言，如中文，英文，波斯文
- Accept-Encoding：客户端可以接受的编码方式
- Accept-Language：客户端可以接受的自然语言
- Authorization：用户名、密码等，服务器使用Authorization验证客户端
- Cookie：服务器生成Cookie发送给客户端，由客户端保存在本地，后续客户端发送请求报文时附带上Cookie，服务器根据Cookie来识别不同的客户端
- Client-IP：客户端机器的 IP 地址
- User-Agent：客户端的名称/类型，常见的是各种浏览器的名称

### 应答报文--起始行--HTTP状态码
应答报文的起始行中包含了HTTP状态码，状态码用来说明HTTP请求的执行结果，如成功或者失败，HTTP状态码共分为5种类型：

状态码范围| 分类
---|---
100 ~ 199 | 信息提示
200~299	 | 成功
300 ~ 399	 | 重定向
400 ~ 499	 | 客户端错误
500 ~ 599	 | 服务器错误

大家常见的200表示执行成功，404表示找不到。

## HTTP GET示例
这里使用有道提供的翻译API，把calculate翻译为中文。在浏览器中输入：

```
http://fanyi.youdao.com/translate?&doctype=json&type=AUTO&i=calculate
```
其中“&doctype=json&type=AUTO&i=calculate”是URL中的query，包括了三个键值对，分别是：
- doctype=json表示翻译结果要求为json格式
- i=calculate表示要翻译的内容
- type=AUTO表示翻译类型，AUTO为自动检测calculate的语言（这里是英文），然后自动选择目标语言（这里是中文）；也可以手动指定翻译类型如type=EN2ZH_CN，手动指明要把英语翻译为中文

浏览器的返回结果为：

```
{
	"type": "EN2ZH_CN",
	"errorCode": 0,
	"elapsedTime": 1,
	"translateResult": [
		[{
			"src": "calculate",
			"tgt": "计算"
		}]
	]
}
```
当要翻译一篇文章时，上述示例就不能用了，因为上述示例中要翻译的内容是放在URL中，而浏览器对URL的长度是有限制的，**文章太长是无法放到URL中的，此时一般会把文章内容放到HTTP主体中而不是URL中**，当然前提是API支持文章内容放到主体中。

## HTTP POST示例
阿里云物联网平台设备接入云端时，支持通过HTTP验证设备的合法性，通过POST把设备信息上传到云端后认证。

```
POST /auth HTTP/1.1
Host: iot-as-http.cn-shanghai.aliyuncs.com
Content-Type: application/json
body: {"version":"default","clientId":"mylight1000002","signmethod":"hmacsha1","sign":"4870141D4067227128CBB4377906C3731CAC221C","productKey":"ZG1EvTEa7NN","deviceName":"NlwaSPXsCpTQuh8FxBGH","timestamp":"1501668289957"}
```
其中
- POST说明使用的Method是POST
- /auth是URL，字面意思是认证
- HTTP/1.1是HTTP版本，客户端要求服务器按照HTTP1/1.1的格式通信
- Host: iot-as-http.cn-shanghai.aliyuncs.com是域名主机
- Content-Type: application/json指明主体的资源类型是json格式
- body是主体，是json格式的字符串，包括productKey产品key，deviceName设备名称等

阿里云给出的应答示例为：
```
body:
{
  "code": 0,//业务状态码
  "message": "success",//业务信息
  "info": {
    "messageId": 892687627916247040,
  }
}
```
应答主体也是json字符串，其他细节请访问 https://help.aliyun.com/document_detail/58034.html?spm=5176.11065259.1996646101.searchclickresult.2f3e18ef7Yyu4v

## URL中的中文
把下面的URL复制到浏览器中

```
http://fanyi.youdao.com/translate?&doctype=json&type=AUTO&i=计算
```
会发现浏览器自动把URL变成了

```
http://fanyi.youdao.com/translate?&doctype=json&type=AUTO&i=%E8%AE%A1%E7%AE%97
```
中文字符串“计算”变成了“E8%AE%A1%E7%AE%97”，这是因为URL中只支持ASCII码，不支持中文，中文需要先使用utf-8编码，并使用%间隔起来。JavaScript中urlencode函数用来编码URL字符串。

## HTTP中的二进制
前面提到过，HTTP是ASCII码字符串，不支持二进制，物联网为了节省数据流量，更倾向用二进制数据，**二进制数据要通过HTTP传输，必须先转换为字符串，常用的base64编码就是把二进制转换为字符串**。

## HTTP/2
HTTP /1.1于1999年发布，HTTP1.1不支持多路复用，即发送一个HTTP请求后，必须等到HTTP应答后才能发送下一个请求，而且HTTP是字符串协议，字符串相比二进制更占用流量带宽。随着Web需求迅猛发展，HTTP1.1急需更新，但是由于HTTP的普及，尾大不掉，要升级HTTP，浏览器，Web服务器，相关程序都需要升级，这可是个大工程！这时Google搞了个SPDY(HTTP/2的前身)，**解决了HTTP多路复用等问题**，并依靠Chrome浏览器大肆推广，鉴于Chrome的市场占用率，SPDY顺利变成了HTTP/2,HTTP/2同时还引入了二进制帧，比字符串更节省流量，HTTP传输效率进一步提升。

如今HTTP/2已经不新鲜了，根据2019年2月对访问量最大的1000万个网站的统计，33.5%已经支持HTTP/2。

### HTTP/3
由于TCP的有序性，可靠性，HTTP 2.0的多路复用遇到了新的问题，如果某帧数据丢失了，TCP必须重传，这会阻塞后续的帧，严重影响多路复用的效率，这是TCP协议本身无法避免的问题，这时Google再次展示了什么叫艺高人胆大，Google在QUIC（HTTP/3的前身）中，使用UDP换掉了TCP！这次步子有点大了，换掉TCP，需要基于UDP实现可靠性传输，并模拟TCP的长连接，最重要的是IPv4中由于NAT的存在，加上UDP无连接的特性，UDP没有连接和断开的标志，路由器只能定期清理的UDP转换信息，否则NAT表会越来越大，这意味着UDP模拟的长连接，可能因为运营商清理NAT而失效；这种情况在IPv6中可能会好点，因为IPv6已经不需要NAT了，但是UDP会不会依旧受到运营商的限制，这并不是Google能决定的。源码先生认为，随着物联网和IPv6的发展，UDP的优势会慢慢凸现，运营商对网络的限制会减少而不是增多，否则不利于物联网行业的发展呢。

对于大部分人，**熟悉HTTP1.1足够了，HTTP/2和HTTP/3虽然技术内部变化很大，但对于应用层的HTTP来说，影响并不大**。

更多HTTP技术细节，源码先生推荐参考《HTTP权威指南》。
