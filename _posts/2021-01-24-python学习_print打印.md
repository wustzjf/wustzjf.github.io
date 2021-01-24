---
layout:     post
title:      python学习_print打印
date:       2021-01-24
author:     周思进
header-img:	
catalog: false
tags:
    - Python
---

初学 python，可能会比较多的用 print 函数来进行调试打印，更多的可能就是去打印相关值对不对，我是这样的~    
下面对此记录下 print 函数的几种打印方式。

先可以看下 python 的帮助手册

![image](https://tva1.sinaimg.cn/large/008eGmZEly1gmyseb9uxuj30vc05ijs5.jpg)


或者直接看[官方手册](https://docs.python.org/zh-cn/3/library/functions.html#print)更好

> print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)

print 会将所有 objects 参数都以字符串的形式写入到 file 文件中（默认是标准输出），objects 各参数之间，会以 sep 隔开（默认空格），最终会再结尾自动加上 end（默认换行）

固简单情况，是像下面这样以拼接的方式进行打印


```
num = 3
print("num=", num)

count =2
print("num=", num, "count=", count)
```

但很明显，这样写是非常别扭的，可以以如下方式，先将要打印输出内容转换成字符串，再用 print 函数进行打印，如下：


```
str = "name=%s, age=%d"%("peiqi", 5)
print(str)
```

实际是可以省去中间变量赋值环节，直接写成如下形式：

```
print("name=%s, age=%d"%("peiqi", 5))
```

到这里应该就明白，其实我们要做的就是怎么字符串拼接方便，就怎么字符串操作，如还可以通过字符串的 format 函数实现拼接操作，如下：


```
str = "name={0}, arg={1}".format("peiqi", 5)
print(str)
```

当然也可以像前面一样省去中间变量赋值环节。
