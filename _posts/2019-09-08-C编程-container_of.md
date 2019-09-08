---
layout:     post
title:      C编程-container_of
date:       2019-09-08
author:     周思进
header-img:	
catalog: false
tags:
    - C编程
---

在前一篇文章《C编程-柔性数组》中学习到如下语句返回的是member成员在数据结构体type中的相对偏移地址

```
&(((type *)0)->member)
```

这也是linux源码中 offsetof 宏的实现方式

```
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)
```

那如果知道了该成员变量的地址，要获取包含该成员变量的结构体的地址呢？

通过如下代码确认结构体的起始地址是成员变量的地址减去成员变量相对起始地址的偏移地址

```
typedef struct 
{
	char *res;
	int count;
}TEST;

int main(void)
{
	TEST test;

	printf("test addr %p\n", &test);
	printf("test.res %p\n", &test.res);
	printf("test.count %p\n", &test.count);

	return 0;
}

// 运行结果如下：
test addr 0x7ffee764ca08
test.res 0x7ffee764ca08
test.count 0x7ffee764ca10

(64位系统，指针存储大小为8字节)
```

这里简单说下，栈的地址空间由高到低进行分配，对于一个数组或者结构体这样的变量，其起始地址是在低地址，最后一个成员是在高地址，从数组取后面的元素，指针是进行加操作也能看出来。

所以计算结构体的起始地址很容易想到是
```
(char *)member - offsetof(type, memeber)
```
这里补充说明下在member前进行(char *)强制转换是必须的，指针的加减操作是根据指针的类型进行偏移的，比如指针类型是int，则指针加1，就是起始地址加上这个类型的地址长度，也就是加4个字节的长度。


不过看下linux源码的实现如下：
```
#define container_of(ptr, type, member) ({			\
	const typeof( ((type *)0)->member ) *__mptr = (ptr);	\
	(type *)( (char *)__mptr - offsetof(type,member) );})
```

会发现，在进行指针计算前，进行了一步指针赋值，粗看感觉这一步好像是多余的，毕竟第二步紧急着又进行类型强转。

那这一步的意义是什么呢？

其实从前面 offsetof 还是 container_of 最后返回的结果，都进行了类型强制转换操作，因为不这样做，编译的时候是会告警的，类型不匹配。

但看const typeof( ((type *)0)->member ) *__mptr = (ptr);这一步，却没有进行类型强制转换操作，这就间接起到了类型检测的效果，学习了！

---

1、typeof 可以获取变量的类型

2、自己手动写下 container_of 宏定义实现！注意宏定义中多语句需要大括号的使用
