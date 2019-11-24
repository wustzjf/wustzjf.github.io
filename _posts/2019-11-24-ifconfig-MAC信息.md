---
layout:     post
title:      网络工具-ifconfig-MAC信息
date:       2019-11-24
author:     周思进
header-img:	
catalog: false
tags:
    - 网络工具
---

在查看网络连通性问题的时候，ifconfig命令基本是必敲的命令之一，可以查看网口名、网口对应的ip地址、mac地址等信息。下面针对eth0网口的一些字段做简单说明（最后准备分几篇文章来写~）。

![ifconfig信息](https://tva1.sinaimg.cn/large/006y8mN6ly1g99bvq4ecoj311y0bcac9.jpg)

HWaddr：即网卡的MAC地址，也是常说的物理地址。
网卡地址一共6字节，即48位，随网卡出厂生产就固定了的，其中前3个字节表示厂商。如果你想知道这个设备是哪个厂家的设备，可以通过MAC地址查询来得知。

MAC地址的特点是具有全球唯一性，当然这是基于出厂就固定不修改的情况下，实际MAC地址是可以通过ifconfig命令来进行临时修改的。

具体操作命令如下：  
ifconfig eth0 down  // 需要先将网卡down掉  
ifconfig eth0 hw ether mac  // 将eth0网卡物理地址设置成mac  
ifconfig eht0 up    // 将网卡重新up生效  

不过MAC地址一般都不会进行手动修改，但工作中也的确会出现设备mac地址发生冲突，或者设备mac地址被交换机给禁了之类的情况，此时可以通过ifconfig命令临时修改下MAC地址来应对下。

像mac地址冲突，一般现象可能是网络包会断断续续的收发，通过抓包分析是可以看出来的，也就是有两个不同的IP地址但mac地址是一样的。

而被交换机禁了，则现象可能是设备端都正常，网线也ok，但就是收发不到数据包。尝试换了下MAC地址，发现就可以正常收发数据了。


上面对MAC地址进行手动设置的时候，需要对网卡进行down操作，如果down了之后，再通过ifconfig命令查看网卡，则看不到对应的网卡信息了。此时可以通过ifconfig -a命令查看，该命令会显示up和down状态的所有网卡信息。

在上图中是可以看到有UP字样的状态显示，处于down状态下的网卡，则是没有UP字样。

而如果想查看网线插口是否稳定，则可以通过dmesg信息查看，是否经常有出现网卡up/down状态。


物理层一般还会检测下网线连接是否ok，上图中的RUNNING字样，就表示有线网络连接正常。对于无线也是一样，WIFI连接成功的情况下，wlan0网卡也会有RUNNING字样，没有连接上，则没有RUNNING字样。


