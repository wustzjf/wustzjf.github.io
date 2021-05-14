---
layout:     post
title:      Linux命令-objdump
date:       2021-05-14
author:     周思进
header-img:	
catalog: false
tags:
    - Linux
---

objdump 命令偶尔会用到下，下面记录一些个人觉得常用的选项说明。

1、查看可执行程序依赖的动态库  

objdump -x  lib.so | grep NEEDED  
-p 也可以  

![image](https://tva1.sinaimg.cn/large/008i3skNly1gqib9znr7kj30u205u0tt.jpg)

-x 选项会显示所有可用的头信息，其效果类似加选项 -a、-f、-h、-p、-r、-t。


<br/>


2、查看符号表信息  

objdump -t test.o

![image](https://tva1.sinaimg.cn/large/008i3skNly1gqic85a14oj30uu0j8q59.jpg)

可以看到想必之前的 nm 命令，objdump 显示的信息更多，且对于所属段更直观


-T 选项打印动态符号表，跟 nm 加 D 选项一样

<br/>


3、查看汇编代码  
$ gcc -g -c test.c  生成汇编文件  
$ objdump -d  -S test.o    

-d：对包含机器指令的段进行反汇编
-S: 选项用于代码和汇编交叉显示，没有 -g  显示效果会差很多

![image](https://tva1.sinaimg.cn/large/008i3skNly1gqicllt0b8j30ue0gytak.jpg)


<br/>


4、指定某个段查看汇编  
一般我们也就查看代码段，如果要查看其他段汇编，需要使用 -D 选项，显示所有段的汇编信息，然后通过 -j 选项指定要查看的段

objdump -D -j .comment -S test.o


<br/>

5、其他一些选项：  
-c 对C++ 符号进行反修饰，增加可读性  
-a 可以查看 静态库含有哪些目标文件组成  
-h 可用于查看目标文件中的段信息  

