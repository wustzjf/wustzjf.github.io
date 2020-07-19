---
layout:     post
title:      Linux命令-uname
date:       2020-07-19
author:     周思进
header-img:	
catalog: false
tags:
    - Linux
---

在下载 frp 工具的时候，对应该选择哪个进行下载有点不知所措... 

![image](https://tva1.sinaimg.cn/large/007S8ZIlly1ggw2cbxf7fj30tw0f2mz5.jpg)

通过搜索简单了解了下，并做下记录。

首先上图中的，darwin、freebsd、linux 指的是操作系统，这个可以通过 `uname -s` 命令来进行查看

如在 MAC 下执行则是   
➜  ~ uname -s  
Darwin

在树莓派下执行则是：  
pi@raspberrypi:~ $ uname -s  
Linux

---

上图后面的 amd64、386（i386）、arm、arm64、mips 则表示的 CPU 架构  

>i386=Intel 80386。其实i386通常被用来作为对Intel（英特尔）32位微处理器的统称。  
AMD64，又称“x86-64”或“x64”，是一种64位元的电脑处理器架构。它是建基于现有32位元的x86架构，由AMD公司所开发。

可以认为 i386 和 amd64 都是 x86 指令集架构，不同的区别是 i386 对应的是32位的，amd64 对应的是64位的。

而 arm 和 x86 的区别主要有 arm 使用的是精简指令集（RISC），而 x86 使用的是复杂指令集（CISC）。

arm 芯片的优势在于功耗低，适用于嵌入式设备，x86 芯片则主要是为了高性能的服务器和主机主机设计的。

arm 和 arm64 从名称上就可以看出是32位系统和64位系统的差别，64位在 ARMv8 上才支持。  

mips 也是用的精简指令集，主要用在嵌入式设备上，如路由器。

---

对于 CPU 架构命令，可以通过`uname -m`命令查看

如在 MAC 上执行则是  
➜  ~ uname -m  
x86_64

那么对应的就是 amd64

而在我的树莓派上执行则是  
pi@raspberrypi:~ $ uname -m  
armv7l

那么对应的就是 arm

---

前面介绍了 uname 命令‘-m’ 和 ‘-s’ 两个选项，下面对其他常用选项做下补充

![image](https://tva1.sinaimg.cn/large/007S8ZIlly1ggw56cqlugj310e09q761.jpg)

可以看到 ‘-a’ 选项是 ‘-s’、‘-n’、‘-r’、‘-v’ 选项的总和，包括系统名称、主机名称、系统发布版本及系统版本详细信息。

