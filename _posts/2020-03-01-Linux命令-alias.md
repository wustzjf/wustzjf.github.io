---
layout:     post
title:      Linux命令-alias
date:       2020-03-01
author:     周思进
header-img:	
catalog: false
tags:
    - Linux
---

工作中如果能经常识别哪些工作是经常重复性在做的，并思考能否将这些重复性的工作自动化，或者变得更简洁，是一件很有必要的事情，可以为此减少不少时间。

在Linux环境下工作，少不了经常敲命令，有些命令会比较繁琐，但又经常会使用到，就可以考虑考虑。

Linux命令alias可以启动设置命令别名的作用，通过该命令可以将一些长命令设置成简单的别名，后面只需要敲简短的别名即执行对应的长命令了。

一般会将alias别名设置写到用户的环境配置文件，如~/.bashrc。

下面简单列举一些具体用法  
1） 打开.bashrc文件  :vim ~/.bashrc  
2） 在文档末尾添加类似如下  
alias cd_work=\'cd ~/dir1/dir2/dir3/workname/\'  
alias ..=\'cd ..\'  
alias ..2=\'cd ../..\'  
alias ..3=\'cd ../../..\'  
3） 保存文档退出 ，并使其生效 ： source .bashrc  

之后只需在shell任何目录情况下输入 cd_work 即可跳转到指定的工作目录。
 


