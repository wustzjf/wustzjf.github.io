---
layout:     post
title:      网络编程-keepalive
date:       2019-12-19
author:     周思进
header-img:	
catalog: false
tags:
    - 网络编程
---

[网络编程-断网重传](https://mp.weixin.qq.com/s?__biz=MzU5Nzk5Njg3OQ==&mid=2247484033&idx=1&sn=4512f54da46cbe5078638cc248e4e407&chksm=fe4ba6a1c93c2fb76a236374735b55ed1078d6fdb04a9bccf9ff0c3c8dfaea56eda5c444b240&token=1134260435&lang=zh_CN#rd)提到一方断网，通信两端是感知不到的，文章举例的客户端则是因为有在数据发送，在重传无果后关闭了连接。

但服务器只是单纯的接收数据，没有进行数据发送，所以它一直维护着这个无效的连接。如果客户端也不发送数据，那两边都一直维护着这个无效的连接。

那要怎么办可以去除这些无效的连接呢？可以通过TCP的keepalive机制来实现。

TCP的keepalive机制可以通过套接字选项SO_KEEPALIVE开启，在2小时内(默认值,可通过TCP_KEEPIDLE选项修改)该套接字上两端都无数据交换(如果中间有数据发送则重新开始计时)，则设置的一方就会发送keep-alive探测包。

1、如果收到了对端的ack响应，则说明对端还在，继续保持该连接。(需要说明这只能表明该连接通信没问题，但不能表明对端一定还能正常收发数据处理，可能更多的还是通过上层应用来发送心跳包确认)


2、如果收到RST包，那说明对端已不在了，该套接字的待处理错误被设置为ECONNRESET。


3、如果没收到响应，则会尝试重发几次(由TCP_KEEPCNT选项控制)，每次发送间隔由TCP_KEEPINTVL选项控制，如果最终都无响应，则也关闭该连接，待处理错误可能为ETIMEOUT或者EHOSTUNREACH。


因此关键几个配置操作如下:

```
 int keepalive = 1    // 开启keep-alive机制
 int keepidle = 1800    // 无数据包交互到发送探测包的间隔时间,单位秒
 int keepinterval = 3   // 开始探测后发送包的间隔时间,单位秒
 int keepcount = 4      // 发送探测包数
 setsockopt(socket, SOL_SOCKET, SO_KEEPALIVE, &keepalive, sizeof(keepalive));
 setsockopt(socket, IPPROTO_TCP, TCP_KEEPIDLE, &keepidle, sizeof(keepidle));
 setsockopt(socket, IPPROTO_TCP, TCP_KEEPINTVL, &keepinterval, sizeof(keepinterval));
 setsockopt(socket, IPPROTO_TCP, TCP_KEEPCNT, &keepcount, sizeof(keepcount));
```

需要注意上述配置都不能设置的过小了，否则可能一个正常的连接也关闭了，或者浪费不必要的带宽来发送这些检测包。


这个选项的使用场景通常是服务器进行设置，因为服务器会比较关注资源的及时释放。当然客户端也可以进行设置，比如发现服务器连接不上了，可以重新尝试连接。

不过两边的处理方式会不大一样，一般而言服务器都是阻塞等待读取操作，所以如果keepalive检测到对端不在了，则读操作返回，并得知相应的错误信息。  

而客户端一般是主动发送，只是保持长连接，有数据时就发送下，并没有select等待读操作，可能就需要单独的检测线程来进行select读操作来判断是否连接断开了，并进行重连等操作。不过想想么，是不是还不如客户端直接尝试发送，如果发送失败了，那再重新建立连接好了。


另外需要注意的是，该错误信息需要通过SO_ERROR选项来获取，可以看[网络编程-SO_ERROR](https://mp.weixin.qq.com/s?__biz=MzU5Nzk5Njg3OQ==&mid=2247484029&idx=1&sn=6630dce5ab33f3a93577877250b04c1b&chksm=fe4ba65dc93c2f4b5eb9da6c4c0dd9a40665eae1ec46b92519057c844c097f36f2ac3d2e8b46&token=1134260435&lang=zh_CN#rd)



UNIX网络编程卷1中图7-6对于TCP连接检测做了汇总，如下图所示：

![TCP连接检测](https://tva1.sinaimg.cn/large/006tNbRwly1ga2g081sg7j31940m6h02.jpg)

