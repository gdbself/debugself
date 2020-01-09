---
title: docker 容器一直重启的解决方法
tags:
  - docker
  - docker重启失败
url: 236.html
id: 236
categories:
  - Docker
date: 2018-12-07 10:26:21
---

**问题描述**
--------

修改了docker容器中的一个文件（这里以一个json配置文件为例），不小心json少了个逗号，造成容器重启失败，然后容器不停的自己重启ing......如何解决这个问题呢，当然是再次编辑下json文件了，but，这个文件没有映射到host主机，只能先进入容器内部，在容器内部修改文件，然而容器启动不了，根本进不去容器内部....

**解决办法**
--------

其实容器内部的文件，肯定也存在host主机的文件系统里，关键的如何找到这个文件呢？执行如下命令

docker inspect your\_container\_id

从输出结果中，找到如下内容

"GraphDriver": {
"Data": {
"LowerDir": "/var/lib/docker/overlay2/1839b86510c923f64efd598c1ca977642ff31169a48195f2eb71cc9c98a6a2af-init/diff:/var/lib/docker/overlay2/05fa64749982113ba331c47b33d58972f20022d4d14f8afb3a4148214d16e6bd/diff:/var/lib/docker/overlay2/b19cd6f8c8b524dd650a68cd3c6925cd002b9cd9a52a3fd664b941a4cb5f5967/diff:/var/lib/docker/overlay2/593fc91f67bfb8373f65bcba1ad97a950b26cc120db7233885c75eb51686d7c0/diff:/var/lib/docker/overlay2/6f6888d4ee5ba12e5209593ad58582d00791b3c70dec12821014f6ab07c7b305/diff:/var/lib/docker/overlay2/c06029dd46581400e62b7357b31f5df1ddf7ad7b8f79131db696ddea1ad1a32f/diff:/var/lib/docker/overlay2/dc60c2cc224b9a8781290057ad3d77ef8b5a7eed91982bffa6f92c9d5b8da423/diff:/var/lib/docker/overlay2/376b18047076d280f1d8fbe802b8617b4d55c3a66b595e5c2c73f958e62a9be3/diff:/var/lib/docker/overlay2/1796c160f61fb9a565f5bfa6e83f0a6124ab73ce8b585afbe93268409dca8055/diff:/var/lib/docker/overlay2/49bfcb592756460b1bdd4ce619c21e6912bc8a285c63004724903b448e9fdc31/diff:/var/lib/docker/overlay2/ab2438cebb4ae0a8632296f1b71612a76579fae1fb829b3e9f133faee4ac1412/diff:/var/lib/docker/overlay2/047991c71f27a4fbe02d70e2b302197e0f1cb63fb2edd923a5075411be7dd8c5/diff:/var/lib/docker/overlay2/bd36cf5f82e66083049df98a8b884c11367cf86f38e51d63e467bdc1918c3794/diff",
"MergedDir": "/var/lib/docker/overlay2/1839b86510c923f64efd598c1ca977642ff31169a48195f2eb71cc9c98a6a2af/merged",
"UpperDir": "/var/lib/docker/overlay2/1839b86510c923f64efd598c1ca977642ff31169a48195f2eb71cc9c98a6a2af/diff",
"WorkDir": "/var/lib/docker/overlay2/1839b86510c923f64efd598c1ca977642ff31169a48195f2eb71cc9c98a6a2af/work"
},
"Name": "overlay2"
}

其实容器内部的文件，就保存GraphDriver--Data指明的那些目录中，其中UpperDir目录中保存了被container修改过的文件，上面示例中为/var/lib/docker/overlay2/1839b86510c923f64efd598c1ca977642ff31169a48195f2eb71cc9c98a6a2af/diff，进入这个目录，修改对应的文件即可

**原理解释**
--------

*   LowerDir：docker image使用的文件，一个docker image可以启动多个docker container，所以多个docker container底层复用了共同的文件，这些文件保存在LowerDir；image中的文件，如果没有被container修改，都在LowerDir中；
    
*   UpperDir：当docker container修改了LowerDir某个文件时，会复制一个副本，修改副本的内容，然后保存到UpperDir中
    
*   MergedDir：复用的LowerDir文件+修改后的UpperDir文件，组成了新的文件，docker container使用的最终文件，就从MergedDir读取。
    

GraphDriver是docker使用的文件系统，docker支持多种文件系统，GraphDriver--Name说明此处使用的Overlay2

overlayfs在linux主机上只有两层，一个目录在下层，用来保存镜像(docker)，另外一个目录在上层，用来存储容器信息。在overlayfs中，底层的目录叫做lowerdir，顶层的目录称之为upperdir，对外提供统一的文件系统为merged。

当需要修改一个文件时，使用CoW将文件从只读的Lower复制到可写的Upper进行修改，结果也保存在Upper层。在Docker中，底下的只读层就是image，可写层就是Container。

![cow.jpg](/images/wp/1544372351572401.jpg "1544372351572401.jpg")