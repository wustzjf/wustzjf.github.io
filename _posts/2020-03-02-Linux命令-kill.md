---
layout:     post
title:      Linux命令-kill
date:       2020-03-02
author:     周思进
header-img:	
catalog: false
tags:
    - Linux
---

在Linux环境下工作，kill命令一般多用来杀死指定的进程，实际是发送了进程退出信号给指定进程。除了退出信号，kill可以指定发送其他信号，可以通过\'-l\'选项查看支持的信号

![image](https://tva1.sinaimg.cn/large/00831rSTly1gcf9ng60nej31400ean02.jpg)

可以看到每个信号都有对应的数字编号，比如要发送SIGALRM信号给进程，则执行如下命令即可:  
kill -14 pid  

如果只是执行\'kill pid\'，则默认发送的是SIGTERM终止信号，有时候你会发现该命令并没能杀死进程，则可以通过执行\'kill -9 pid\'来杀死进程。

可见SIGTERM和SIGKILL信号是有区别的。  
通过man手册可以看到在KILL信号描述里特意说明了“cannot be blocked”，即这个信号是不能被捕捉处理的。

而SIGTERM则可以捕捉这个信号，比如父子进程，父进程如果会多次启停子进程，两者之间又没有进程间通信进制，父进程可以通过发送SIGTERM信号给子进程，子进程捕捉接收该信号后，可以先释放申请的资源，然后再退出进程，避免了内存等资源的泄露浪费。

上面kill命令在发送信号给指定进程前，都需要先知道进程的pid号，这个可以通过ps命令查看。

如果你知道进程名称，你也可以通过killall命令直接通过进程名来杀死进程，基本用法一样，比如杀死进程test:  
killall -9 test


