---
layout:     post
title:      Linux命令-echo
date:       2020-03-29
author:     周思进
header-img:	
catalog: false
tags:
    - Linux
---

echo命令在shell环境下或者shell脚本里都是非常常用的一个命令，下面举几个例子进行简单说明。

echo的选项并不多，日常可能基本不用选项，或者使用下面这个选项  
-n : 不输出结尾的换行符

![image](https://tva1.sinaimg.cn/large/00831rSTly1gdb75vbnzoj30v805uaag.jpg)

从图中可以看出明显的输出差异。

比如使用openssl计算\"hello\"字符串的MD5值，记得一定要带-n选项，否则计算的值是完全不同的，如下:  
![image](https://tva1.sinaimg.cn/large/00831rSTly1gdb7i2wahdj30wy04gmxu.jpg)


如果想清空某个文件内容，则可以执行如下命令：  
echo -n > file

想往某个文件写内容也是一样的方式  
echo "hello" > file    &nbsp; &nbsp; &nbsp; &nbsp;   # 覆盖原有内容，从头开始写  
echo "yzsijin" >> flle  &nbsp; &nbsp; &nbsp;  # 在之前内容的第二行开始写，因为前面有换行

