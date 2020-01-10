---
title: docker容器中文件一直是旧版本的原因
tags:
  - Docker
  - docker-compose
  - volume
url: 154.html
id: 154
categories:
  - Docker
keywords: Docker容器,云原生,k8s,微服务,云平台
description: 'docker容器中文件一直是旧版本的原因'
date: 2018-03-23 17:00:16
---

开发emqtt插件，我已经更新了emqtt的代码和配置文件，在自己本机部署到docker里面，一切正常，测试通过后把emqtt push到服务器。

但是服务器pull新版本后，发现docker容器中的emqtt却没有更新，一直是旧的；

我把服务器端的emqtt容器commit+save为文件，并导入到自己本机，emqtt中的文件又变成新的了，要不要这么诡异？

经过将近两天的不断测试，终于发现了问题原因：
----------------------

在emqtt的Dockerfile中，通过VOLUME把emqtt文件夹映射到宿主机上，而宿主机的emqtt文件一直是旧版的，为什么宿主机的文件是旧版的呢？

经测试，通过docker-compose up启动容器时，虽然使用新的Tag，容器也recreate了，但是VOLUME使用的宿主机的文件却没有recreate

解决办法：先通过docker-compose down删除容器，或者docker rm删除容器后，再启动新容器。

  

参考资料

https://blog.csdn.net/lindev/article/details/51452574

https://segmentfault.com/q/1010000008704052/a-1020000008706890