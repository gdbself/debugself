---
title: Erlang中的code path搜索路径
tags:
  - Erlang
url: 124.html
id: 124
categories:
  - Erlang
date: 2017-11-29 16:39:29
---

**code path搜索路径**  
每一个module/library对应一个目录/路径，erlang会从这些目录搜索需要的代码；通过code:get_path()可以得到这些路径。  
搜索路径包括：

*   当前目录
    
*   $OTPROOT/lib，即OPT的安装目录，通过code:root_dir()可以获取该目录
    
*   系统环境变量ERL\_LIBS，也可以用来定义库目录；ERL\_LIBS可以包含多个目录，Unix用冒号分割，Windows用分号分割
    
*   通过code:add_patha(Dirs)或者erl -pa添加自定义的目录;注意这里的目录是ebin的目录，不是源码的目录；该目录支持相对路径  
      
    In other words, modules found in any of the additional library directories override modules with the same name in OTP, except for modules in Kernel and STDLIB.  
      
    http://erlang.org/doc/man/code.html