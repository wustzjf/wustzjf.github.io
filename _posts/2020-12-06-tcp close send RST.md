---
layout:     post
title:      tcp close send RST
date:       2020-12-06
author:     周思进
header-img:	
catalog: false
tags:
    - 网络编程
---

一直以为网络套接字进行 close 的时候，就是发送 FIN 包给对端，但发现有时候发送的却是 RST 包，通过内核源码查看，如果 close 套接字的时候，接收缓冲区还有未读数据，则是发送的 RST，源码中有如下注释：

> /* As outlined in RFC 2525, section 2.17, we send a RST here because
	 data was lost. To witness the awful effects of the old behavior of
	 always doing a FIN, run an older 2.1.x kernel or 2.0.x, start a bulk
	 GET in an FTP client, suspend the process, wait for the client to
	 advertise a zero window, then kill -9 the FTP client, wheee...  
	 Note: timeout is always zero in such a case.
	 */

通过模拟并抓包查看，的确如此：

![image](https://tva1.sinaimg.cn/large/0081Kckwly1gleg8lynmmj31pi084n1a.jpg)

上图是客户端连接服务器后发送2个字节数据，服务器接收1个数据后进行 close 操作，服务器发送了 RST 结束了连接。

而如果是服务器正常接收2个字节数据后再 close 操作，则是正常的四次挥手操作，如下

![image](https://tva1.sinaimg.cn/large/0081Kckwly1glefw6gjevj31ha0bc0xm.jpg)


<br/>

那如果服务器有未读数据情况下，不是发 RST，而是发 FIN，会有什么影响呢？ 从源码注释了解可以看 RFC 2525, section 2.17。

> The
      problem is less serious if any subsequent data sent to the now-
      closed connection endpoint elicits a RST (see illustration below)
      
如果对端会继续发送数据给已关闭的套接字，则会接收 RST 报文，该情况下，问题并不严重。

> Failure to reset the connection can lead to permanently hung
      connections, in which the remote endpoint takes no further action
      to tear down the connection because it is waiting on the local TCP
      to first take some action.  This is particularly the case if the
      local TCP also allows the advertised window to go to zero, and
      fails to tear down the connection when the remote TCP engages in
      "persist" probes (see example below).

但如果关闭方接收窗口为0了，则对端只能等待关闭方更新接收窗口后才能继续发送数据，而实际关闭方如果 close 不丢弃读取缓冲区中的数据，则永远不会更新接收窗口，导致连接无法释放。

如果 tcp 在关闭套接字时丢弃未读数据，更新窗口，那么对端会继续发送数据，此时就会收到 RST 报文，但没有直接发送 RST 报文好。

<br/>

不过收到 RST ，你可能没法直接确认到底是哪里出了问题，毕竟触发发送 RST 的场景有多种。有什么办法来排查网络通信是正常的，只是应用层面设计不合理导致的问题呢。

这个可以考虑从 recv/read 的返回值入手。  
如果是收到 RST 报文，则表示连接重置，已不存在，对方不会再收发任何数据，read 接口收到该报文，会返回出错，值为-1。  
而如果是收到 FIN 报文，则只是表示对方不再发送数据给你，但仍旧可以接收数据。 read 接口收到该报文，则返回值为0。

shutdown 接口可单独关闭发送通道，这样即使接收缓冲区有未读数据，也会正常发送 FIN 包给对端，对端接收接口正常返回，没有出错，但可以检测到读取的数据长度为0，则至少排除了对端崩溃等情况，可以从应用数据发送层面来进行排查问题。

下图是在套接字有未读数据情况下，先进行 shutdown 关闭发送通道，再进行 close 的抓包（shutdown 并不会释放套接字，最后必须通过 close 操作释放）：

![image](https://tva1.sinaimg.cn/large/0081Kckwly1glefbtraryj31ts0a2gra.jpg)

而如果通过 shutdown 先关闭接收通道，再进行close，则最后体现是正常的四次挥手操作，对端是没法再捕捉到异常的。因为 shutdown 在关闭接收通道时，任何接收缓冲区中的数据都将被丢弃，这样再进行 close 操作的时候，也就没有未读取的数据，不会发送 RST 报文了。


