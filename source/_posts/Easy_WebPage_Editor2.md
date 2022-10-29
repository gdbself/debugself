---
title: 开发了一个辅助摸鱼的Chrome小插件 
tags:
* Chrome extension
* 摸鱼
keywords: Chrome插件,Chrome extension,摸鱼
description: 'Chrome插件开发教程,上班摸鱼'
date: 2022-10-29 11:00:00
---

# 开发了一个辅助摸鱼的小插件

参加生财有术的英文工具站航海，由于自己对英文环境了解不是太多，所以给自己定的目标比较低，跑通上架Chrome插件的流程即可，不考虑变现等需求，最后成功上架了一款真免费，纯用爱发电的Chrome插件。

这个插件主要用来方便摸鱼，比如上班时一直浏览生财有术的网站，网站左上角是生财的logo，明显不务正业，特别是公司电脑装有监控软件，会对电脑截屏，所以想把网页上一些明显不务正业的内容删掉，让网页看起来像是在工作。

虽然使用Chrome自带的开发者工具也能删除网页内容，但是每次都先调出开发者工具，然后再一个个删除，有点麻烦，而我这个插件要实现的就是“鼠标点哪里，就删哪里”.
![Delete Mode](/images/wp/chrome_editor_delete.png)
上图中进入“Delete Mode”后，当鼠标移动时，会自动识别HTML元素并显示一个蓝色的边框，此时点击鼠标左键，即可直接删除蓝框内的所有内容。
![Editor Mode](/images/wp/chrome_editor_edit.png)
另外，如上图进入“Edit Mode”后，可以像Word一样对网页进行编辑，至于用途嘛，先修改网页的内容，然后截图或者把网页导出为pdf，把截图发给别人，以假乱真。

## 插件安装地址

*   Chrome安装地址(需梯子)：<https://chrome.google.com/webstore/detail/easy-webpage-editor/onmafkjgockcgciifomkeccldlfieblf>
*   Edge安装地址(无需梯子)：<https://microsoftedge.microsoft.com/addons/detail/easy-webpage-editor/aoceegjdgobcnmokmjhpkcbeeoojfnen>
*   离线包下载地址：<https://wwm.lanzoul.com/b03vejhej> 密码\:bf6y

## 插件开发记录

### 需求调研

需求调研是最重要的，不然很容易自嗨，但是也是最难的，上次Chrome插件航海，21天都花在了验证需求上了，需求是挑来挑去，否定来否定去，最后海航结束，需求被扔一边了.....所以这次英文工具站航海，降低了自己的目标，先跑通插件上架流程，至于需求......我已经把各位教练大佬的分享记到小本本上了，大佬们讲的太干货了，我后面加点水慢慢消化。

### 基础知识

需要用到前端HTML+JS+CSS相关的基础知识，这个就不多说了。

### Hello World

Chrome官方提供的示例：<https://github.com/GoogleChrome/chrome-extensions-samples>，其中examples/hello-world目录下是Hello World，官方的示例简单且简陋，基本是羊了个羊的第一关，而上架插件的复杂度，一般属于第二关。

另外也可以参考下Edge官方提供的示例 <https://github.com/microsoft/MicrosoftEdge-Extensions> 

### 入门教程

Chrome官方英文文档：<https://developer.chrome.com/docs/extensions/mv3/getstarted/>

找了两篇不错的中文入门教程，把开发Chrome插件基本概念过一遍

*   <https://juejin.cn/post/7114959554709815326>&#x20;
*   <https://www.cnblogs.com/liuxianan/p/chrome-plugin-develop.html>

### 实现自己的插件

如何快速开发自己的插件，当然是学(chao)习(xi)竞品的代码了。

前端的代码想要防止别人抄袭，最多是做下混淆，可是Chrome官方不允许提交的Chrome插件混淆，见<https://developer.chrome.com/docs/webstore/program_policies/#code-readability>，美其名曰为了防止恶意代码，不允许混淆/加密插件的代码，但是可以Minification，即可以缩短变量名，去除多余空格之类，方便减少插件的大小，即使Minification后的代码，也能被被人看出个七七八八了

**如何获取竞品的代码呢？**

通过Extension Source Downloader这个插件：<https://chrome.google.com/webstore/detail/extension-source-download/dlbdalfhhfecaekoakmanjflmdhmgpea>，可以把Chrome Web Store上的插件的源码下载下来，我下载了几个竞品的代码，有些代码连Minification都没做，源码面前，了无秘密。

这里有一个是否侵权的问题，最好是参考别人的思路，代码自己写。考虑到竞品是免费插件，我这个也是免费插件，小工具不涉及商业利益，所以我直接引用了不少竞品的源码，同时也想测试下Chrome的审核程度，测试结果是我这个插件成功上架了，不过我引入Bootstrap和Jquery第三库，代码总量上看改动挺大，但是Chrome审核时应该把第三方库过滤掉的

### 注册开发者账号

开发者账号注册地址 <https://chrome.google.com/webstore/developer/dashboard/>

需要支付5美元，虽然看网上说使用国内Visa卡支付会遇到问题，我测试使用中行Visa单币信用卡秒过。

选择账号地址时，没有China可选择，参考网上的方法，选择Hong Kong，地址填：转大陆广州市XXX，没遇到问题。

### Privacy Policy

Privacy Policy主要是关于软件收集个人隐私的法律要求，比如欧盟的GDPR条例规定所有App，网页等软件，必须有Privacy Policy，具体咱也不清楚，网上随手搜了一个Privacy Policy在线生成工具：

<https://www.privacypolicygenerator.info/>，测试了下还挺方便的，而且会生成个人专属的网页地址，连网页托管都省了，把生成的地址添加到个人账号的Privacy Policy中即可。我这里是把生成的网页，挂在了我自己的博客网站上。

![privacy policy](/images/wp/chrome_editor_privacy_policy.png)

上图中Trader declaration，初步认为用来声明自己是否是交易者，是否对提供的服务收费，我这里选择的是非交易者。

### 上架到Chrome

上架要填写的内容挺多的，插件描述，插件icon，插件截图等。

还需要填写插件需要的权限permissions：<https://developer.chrome.com/docs/extensions/mv3/declare_permissions/>，而且每个权限都要填写使用该权限的理由，很是麻烦，手动使用google翻译把中文理由翻译成英文，贴出来仅供参考。

```
The user selects the font color, font size and so an,these selected info are saved in Local storage so that this data can be used again in the background.js and popup.

This extension help user to edit the content of web page,when user active one tab,the extension use chrome.scripting.executeScript to inject js to webpage,Only then the user can edit the webpage

This extension help user to edit the content of web page,when user active one tab，the extension need to know which tab is actived,thus help user edit the actived webpage

```

最后提交，大约1个小时就审核通过了，估计功能太简单，所以比较快通过了。

### 上架到Edge

先按照 <https://docs.microsoft.com/zh-cn/microsoft-edge/extensions-chromium/publish/create-dev-account>注册微软系的账号，然后访问

<https://partner.microsoft.com/zh-cn/dashboard/microsoftedge/overview> 提交Edge插件，Chrome相关的内容原封不动的填写到Edge中，大约1天后也审核通过。

