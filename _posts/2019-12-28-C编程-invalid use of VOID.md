---
layout:     post
title:      C编程-invalid use of VOID
date:       2019-12-28
author:     周思进
header-img:	
catalog: false
tags:
    - C编程
---

日常开发都是基于C语言编程，没怎么接触C++，刚好遇到需要在C++代码中调用C编程的代码块，编译出现如下错误提示：  

```
error:'<anonymous>' has incomplete type  
error:invalid use of 'VOID {aka void}'
```

在Stack Overflow上搜到有类似提问的，看有人回复  

> error: empty parameter list defined with a typedef of 'void' not allowed in C++

即C++不允许入参为空的情况使用typedef定义的void类型

测试代码如下：


```
//fun.h
#ifndef __H_FUN_H
#define __H_FUN_H

typedef void VOID;
VOID fun(VOID);

#endif


//fun.c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "fun.h"

void fun(void)
{
	printf("fun\n");

	return ;
}


// test.cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#ifdef __cplusplus
extern "C" {
#include "fun.h"
}
#endif

int main(void)
{
	fun();

	return 0;
}

```

执行 gcc fun.c test.cpp 就会出现如下错误  

```
fun.h:5:10: error: ‘<anonymous>’ has incomplete type
fun.h:5:14: error: invalid use of ‘VOID {aka void}’
```

只将fun.h头文件的入参VOID修改为void后重新编译就正常了  


这说明的确只是入参声明有影响，返回类型是VOID并不会报错，另外fun.c里的定义实现入参声明VOID也并不影响，所以只需要保证C++代码编译包含的头文件中不存在这样的声明方式即可。


另外就是需要注意C++代码调用C代码的接口，需在包含的头文件使用extern "C" 关键字代码，否则编译就会提示如下错误  

```
undefined reference to `fun()'
```
