---
layout:     post
title:      arp相关配置-arp_ignore等
date:       2019-12-04
author:     周思进
header-img:	
catalog: false
tags:
    - 网络工具
---

Linux系统有以下五个arp相关的配置参数：  
arp_accept  
arp_notify  
arp_ignore  
arp_notify  
arp_filter  

这些参数在多个位置可进行设置  
/proc/sys/net/ipv4/conf/all/arp*  (所有接口)  
/proc/sys/net/ipv4/conf/default/arp*  （后面的接口？）  
/proc/sys/net/ipv4/conf/eth0/arp*  （特定接口）

因为有多个地方可配置，但最终则根据这些配置中的最大值来生效


上面这些参数是在/proc/sys/下面的，所以参数说明可以根据前面文章[资料搜索-内核参数查询](https://mp.weixin.qq.com/s?__biz=MzU5Nzk5Njg3OQ==&mid=2247483999&idx=1&sn=20c61411d92c9c00d0e1a77a42a641ac&chksm=fe4ba67fc93c2f69fc5d5f28f97a3c95d42f4512f6aee975654a5413ecc79a52c20ac12af2e9&token=1528447463&lang=zh_CN#rd)搜索查看，下面针对这些选项做简单说明。

#### arp_accept  
默认为0，则在当前没有对端mac缓存的情况下，设备收到免费arp是不会更新的，只有在有缓存的情况下才会更新。  
如果设置为1，则即使没有缓存记录，收到免费arp就会更新


#### arp_notify  
默认为0，不执行任何操作  
如果设置为1， 则设备起来或者网卡MAC地址更改的情况下，会免费发送arp包


#### arp_ignore  
默认为0，从网卡1收到arp请求，该请求询问的是网卡2的ip地址，网卡1也会进行arp应答，但应答的mac地址是网卡1的mac地址。

这里就需要补充说明下，对于Linux而言，IP地址属于主机的，而不是属于特定的网卡。

如果设置为1，则只有查询目标IP就是接收网卡的IP时才会做应答  
如果设置为2，则除了查询目标IP是接收网卡的IP外，还需要检测发送者的IP地址和该网卡是同一子网才做应答


#### arp_filter 
默认为0，如果设置为1，该参数在arp_ignore的基础上，还会检查下数据包如果发回发送方，根据路由表是否仍从该网口出去，如果是的话，就进行应答，如果不是的话就不进行应答。


#### arp_announce  
默认为0，允许使用任意网卡的IP地址作为arp源IP地址  
如果设置为1，则尽量避免使用不属于该发送网卡的本地地址  
如果设置为2，则忽略发送方的源IP地址，而是选择该主机最适合的本地地址作为arp源IP地址。

比如设备有两个网卡，分别是A网段和B网段的一个地址，现在设备要发送的数据包指定源IP地址是A，目标地址是同B网段的一个地址，根据路由则会从B网卡发送arp查询目标mac地址；如果参数设置的是2，则arp请求源IP会是B网卡的实际IP，而如果参数是0，则arp请求源IP会是A网卡的IP，这样对端缓存的arp也就是错乱的。

