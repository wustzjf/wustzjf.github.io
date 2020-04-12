---
layout:     post
title:      Linux命令-wget
date:       2020-04-12
author:     周思进
header-img:	
catalog: false
tags:
    - Linux
---

开发人员偶尔需要从网上下载一些开源代码使用，可以直接在官网网页端进行下载即可，今天要介绍的命令也可以完成同样的工作。

wget命令用于从指定的URL下载文件，其设计适用于不稳定网络情况下的文件下载，当因为网络原因下载失败，会不断的进行尝试直到整个文件下载完；如果服务器支持断点续传，wget可以选择从上次停止的地方继续开始下载。

下面以下载openssl为例，wget需要指定下载文件的URL，所以先去官网获取目标文件的下载地址。

来到openssl官网的源码下载页
https://www.openssl.org/source/

右键点击要下载的目标文件，复制下载链接地址

![image](https://tva1.sinaimg.cn/large/007S8ZIlly1gdr2dajxvmj31860a2k0x.jpg)


然后本地跳转到期望存储目标文件的目录下，执行如下命令即可：  
wget https://www.openssl.org/source/openssl-1.1.1f.tar.gz

下图是下载完的示意图
![image](https://tva1.sinaimg.cn/large/007S8ZIlly1gdr2iewd90j31960aognx.jpg)


如果中间因为网络等其他原因发生中断停止了下载，则可以通过如下命令继续从上次下载的地方继续  

wget -c https://www.openssl.org/source/openssl-1.1.1f.tar.gz


wget除了支持HTTP和HTTPS协议外，还支持FTP协议下载。FTP登入交互式场景需要用户手动输入账户和密码，而wget是非交互工作模式，可以直接在命令行指定FTP账户和密码，则非常适合脚本里执行FTP文件下载操作。

下面是wget下载FTP文件的命令：  
wget -nH --ftp-user=username --ftp-password=password  ftp_url

FTP默认使用被动连接方式，如果想使用主动连接方式，可以通过--no-passive-ftp选项设置，从输出打印上就可以看出差别，如下图所示：

![image](https://tva1.sinaimg.cn/large/007S8ZIlly1gdrbld4wjyj318y0rq42o.jpg)


