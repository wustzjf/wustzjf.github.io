---
layout:     post
title:      Linux下通过man查看pthread_mutex_init
date:       2019-08-02
author:     周思进
header-img:	
catalog: false
tags:
    - Linux
---

前面Linux帮助命令有提到可以通过'man -k pthread'查看线程相关接口，实际发现搜索结果中并没有线程相关锁接口，比如pthread_mutex_init等。



我使用的是Ubuntu系统，网上搜索了解原来Ubuntu系统默认是没有完全安装man手册的，可以通过如下命令进行手动安装：

```c
sudo apt-get install glibc-doc  manpages-dev manpages-posix-dev
```


安装完之后，再通过'man -k pthread'就可以看到新增了很多pthread相关接口。