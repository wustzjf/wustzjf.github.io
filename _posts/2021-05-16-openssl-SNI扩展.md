---
layout:     post
title:      openssl-SNI扩展
date:       2021-05-16
author:     周思进
header-img:	
catalog: false
tags:
    - openssl
---


阅读文章：
https://en.wikipedia.org/wiki/Server_Name_Indication  

通过文章了解了下 openssl SNI 的用途。  
为了节省服务器硬件成本，通过虚拟主机技术可以让用户在一台服务器同时运行多个网站。HTTP 协议访问可以通过 hostname 字段来区分是要访问哪个服务。但为了访问安全性，会考虑所有提供的服务都支持 HTTPS 访问，这就需要配置对应的证书。

因为有多个服务网站，如果想多个服务网站配置同一个证书，可以使用通配符证书，但通配符证书仅支持匹配一级子域名；还有一种方法则是在证书中直接指定多个具体域名（subjectAltName字段实现），但这种方法的弊端就是如果主机又多了一个服务网站，需要重新颁发证书。那如果新增的服务网站就配置自己的证书会有什么问题呢？

HTTPS 因为需要先建立 tls 安全链路后才能发送报文数据，即在发送请求报文前，就需要服务端返回对应的证书以建立连接，这就不能像 HTTP 协议那样通过 hostname 来指定要访问哪个服务网站。如果服务主机不能明确客户端要访问哪个网站，可能就返回一个默认证书。但这个证书中的域名实际和客户端要访问的不一致，客户端就可能直接终止连接。

而 SNI 扩展字段，则可以在客户端发送 client hello 请求时就明确所要访问的主机域名，这时服务主机就可以正确返回对应的证书，成功建立通信连接。


命令测试：  
openssl s_client -connect testname.com:443 -servername testname.com


代码实现：  
客户端只需要在建立 SSL 连接之前，调用  SSL_set_tlsext_host_name(ssl, servername) 接口设置下即可。

