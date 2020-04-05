---
layout:     post
title:      Linux命令-hexdump
date:       2020-04-05
author:     周思进
header-img:	
catalog: false
tags:
    - Linux
---

之前有推过[Linux二进制查看命令-xxd](https://mp.weixin.qq.com/s?__biz=MzU5Nzk5Njg3OQ==&mid=2247483932&idx=1&sn=62fa08d21ce77e038eee00d033b898cb&chksm=fe4ba63cc93c2f2adb6925d7b8fd6164d185a8dd5a23b5b13862cf29f21990a3aa90027d664c&token=49845064&lang=zh_CN#rd)

今天再推荐一个二进制查看命令-hexdump

hexdump一般可能更多的也是用于显示二进制文件的十六进制编码吧  
如下两个命令输出显示上是差不多的  

$hexdump -C file  
$xxd file

![image](https://tva1.sinaimg.cn/large/00831rSTly1gdj8it5fmuj31460b0myo.jpg)

上图是hexdump命令的输出，如果不加-v选项，则对于跟前面相同输出的所有行以"*"显示


hexdump也可以转成十进制、二进制、八进制等，只是如果只使用-b、-d等选项查看，输出挺不友好的。可以通过-e选项来指定输出格式，如下图简单的使用：

![image](https://tva1.sinaimg.cn/large/00831rSTly1gdj9fotrkjj31ik0ba40r.jpg)

输出格式以单引号包含，其格式组成可分3部分，分别以空格隔开。形式如 “x/y format1 format2”，表示每y个字节按format1格式输出，每x个字节按format2格式输出，其中x和format2可以省略。

看上图最后一个执行命令，则4/1表示每1个字节按“%02x   ”的形式输出，每4个字节按“\n”的形式输出，也即4个字节后进行换行。  

