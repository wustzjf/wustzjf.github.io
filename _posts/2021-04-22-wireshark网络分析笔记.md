---
layout:     post
title:      wireshark网络分析笔记
date:       2021-04-22
author:     周思进
header-img:	
catalog: false
tags:
    - 网络工具
---

以前做的笔记，翻出来再温习下。

本文是阅读《wireshark网络分析就这么简单》和《wireshark网络分析的艺术》做的一些笔记。  


技巧篇  
1、如只要分析ip头或tcp头，可减少每个抓包数据的大小，通过设置limit each packet to的值即可（capture->options->双击抓包网卡）


2、设置包的颜色 view —> Coloring Rules  


3、edit—>preferences 在此窗口单击 protocols -> tcp。比如经常要对sequence number做加减运算，不妨选中relative sequence numbers  


4、保存时选择displayed要慎重考虑，最好就保存抓取的所有包，查看的时候过滤  


5、可通过ctrl+f搜索关键字，比如怀疑包中有”error”一词，选中”string”单选按钮，然后在filter中输入”error”进行搜索  


6、wireshark提示 
- ［packet size limited during capture］ 标记这个包没有抓全 
- ［tcp previous segment not captured］网络包没有抓到：一种是真的丢包了，一种是抓包工具没有抓到。看对方回复的确认ack就能判断 
- ［tcp asked unseen segment］ 几乎可以忽略，只抓到了后面的ack，没有抓到前面的数据包 
- ［tcp out-of-order］乱序 
- ［tcp dup ack］发生乱序或丢包了 
- ［tcp fast retransmission］ 发送方收到3个或以上tcp dup ack，进入快速重传 
- ［tcp retransmission］发生丢包，又没有后续包触发dup ack 
- ［tcp zerowindow］ win= 表示发送方当前还有多少缓冲区可以接收数据 
- ［tcp window full］ 发送方已经把接收方的窗口耗尽了，不能再发送数据了 
- ［tcp segment of a reassembled pdu］ 这是启用了 edit—>preferences—>protocols—>tcp 菜单里的allow sub dissector to reassemble tcp streams，在最后一个包把所有的包虚拟的集中起来，方便复制整个应用层的edu （copy—>bytes—>printable text only） 
- ［continuation to #］则是上面选项没启用 
- ［time-to-live exceeded］未收全包 无法组装  


7、查看包的详细信息中的seq/ack analysis —> bytes in flight 表示的是拥塞窗口。如处于拥塞避免阶段，则是累加一个mss；如果是慢启动阶段，那就是成倍增加。  


8、性能问题分析 
- statistics->summary （查看统计信息，比如平均流量） 
- statistics->service Response Time->ONC-RPC->program:NFS Version:3—>create stat（衡量服务器性能） 
- analyze—>expert info composite （查看重传统计、连接的建立、重置统计等等） 
- statistics->tcp stream graph->tcp sequence graph （生成统计图，考虑选择时间的长度）  


9、wireshark对应的命令行工具tshark，例： 
tshark -r tcpdump.log -R “ip.addr == 192.168.1.134” -w tcpdump.log.filtered 
更多见官方文档  


10、查看三次握手的mac地址是否是正确的，有可能网络的拓扑结构出现问题。比如发出去的包进过网关，回来的包没有过网关。  


11、tcp.analysis.ack_rtt > 0.2 and tcp.len == 0 过滤是否存在延迟包  


12、握手失败一般分两种类型，要么被拒绝，要么是丢包了。 
表达式1: （tcp.flags.reset == 1） && (tcp.seq == 1) 
表达式2: （tcp.flags.syn == 1） && (tcp.analysis.retransmission)  


13、包在服务器上抓的，网络上又存在延时，顺序可能跟客户端上看到的不一样。比如四次挥手协议。因此在两端抓包排查问题更好。  


14、计算网络拥塞点：发生拥塞时的在途字节数即是该时刻的网络拥塞点。 
找出重传包，再根据其seq找出原始包（tcp.seq == ），根据公式：在途字节数＝seq+len-ack  


15、只见重传包，不见原始包，可能原因： 
- 这个包是在接收端抓的，看不到已经丢失在路上的数据包是正常的 
- 开始抓包的时候，原始包已经传完了 
- wireshark出了bug，把一个正常包标记成重传包了 
- 如果是在发送端抓的，要考虑是否启用了LSO（large segment offload）；该功能对于大于mss的数据包不由tcp层分段（减少cpu功耗），转由网卡处理分包操作，在发送端抓的包则是未经分段的大包（包含丢失了的分段包，tcp.seq < 丢包的seq确认下）  


16、可以用netstat命令查看当前的网络连接状态  


17、留意包的ttl值，可确认包经过了多少跳。  


18、 
在option字段中的window scale字段声明一个shift count，把它作为2的指数，再乘以tcp头重定义的接收窗口，就得到真正的tcp接收窗口了。 
wireshark必须要抓到三次握手，才能计算这个win值。有些时候防火墙识别不了window scale，对方也就无法获得shift count，导致严重的性能问题。

