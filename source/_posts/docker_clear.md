---
title: docker占用空间太大的清理方法
tags:
  - docker
url: 156.html
id: 156
categories:
  - Docker
date: 2018-03-27 16:02:30
---

docker system df 查看docker空间占用情况

docker system df -v 查看空间占用细节

docker rm container_id 删除不需要的container

docker rmi image_id 删除不需要的image

docker system prune 自动空间清理

**但是，经测试，docker system prune 只清理掉很少的空间  
docker system prune --volume 这个清理了很多空间  
**