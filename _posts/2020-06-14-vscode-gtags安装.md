---
layout:     post
title:      vscode-gtags安装
date:       2020-06-14
author:     周思进
header-img:	
catalog: false
tags:
    - 工具
---


vscode 原生支持的查看所有引用的地方并不全，可以通过安装 gtags 插件解决。

MAC 系统安装  


```
brew install global
```

安装完之后，只需要在工程源码目录下执行 gatgs 命令，就会生成 GPATH（路径数据库）、 GRTAGS（引用数据库）、 GTAGS（定义数据库） 三个文件。

这三个文件总大小差不多会占源码文件总大小的 2/3 样子，所以也挺占磁盘空间。

有了这三个文件之后，就可以通过 global 命令快速的查找定义和引用等。

<br/>

1、查找某个函数的定义  

```
global -x func 
```
 
这里 func 可以是具体的函数名，这样查找的就是指定的函数；也可以使用正则表达式，如 'func*'，这样查找的就是以 func 开头的函数

选项 x 用于显示更具体的细节，如具体到在哪个文件的多少行及函数的具体声明

![x选项](https://tva1.sinaimg.cn/large/007S8ZIlly1gfs2kcyseej312804cmxy.jpg)

这里显示的路径是当前执行 global 命令的相对路径，如果想显示这个文件的绝对路径，可以使用 -a 选项。

这个命令可以在工程目录下的任何路径执行，但都会给你到整个工程下去查找，如果要限定查找目录，比如只查找当前目录下的文件，则可以使用 -l 选项。


<br/>

2、查找包含特定字符串的代码  

```
global -xg 'string'
```

![g选项](https://tva1.sinaimg.cn/large/007S8ZIlly1gfs33tmwxnj318q05u0ud.jpg)

这和使用 grep 命令差不多

<br/>

3、查找引用该函数的代码  
```
global -xr func
```

<br/>



更详细的使用操作见官方文档：  
https://www.gnu.org/software/global/globaldoc_toc.html

