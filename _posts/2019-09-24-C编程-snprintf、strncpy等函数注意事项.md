---
layout:     post
title:      C编程-snprintf、strncpy等函数注意事项
date:       2019-09-24
author:     周思进
header-img:	
catalog: false
tags:
    - C编程
---

工作中经常需要用到snprintf、strncpy等类似接口做字符串操作，发现很多同事也一样总是搞不清，是否有\0结尾等，有人索性将目标地址长度调用接口时做了减1处理，例如：

```C
/** brief		获取用户名  
 * @param[in]	name_len : 存储长度，需要能够存储\0字符
 * param[out]	name_sz : 保证\0结尾
 * return		名字长度
 */
int get_name(char *name_sz, int name_len)
{
	int len = 0;
	char *str = "12345678";

	// 省略入参检查

	// 这个例子举的不大好，正常也是用strncpy做字符串拷贝操作
	// snprintf更多用来组装字符串
	len = snprintf(name_sz, name_len - 1, "%s", str);

	return len;
}

int main(void)
{
	char name[NAME_LEN + 1] = {0};
	int len = 0;

	len = get_name(name, sizeof(name));
	printf("len = %d\n", len);
	printf("name = %s\n", name);

	return 0;
}

//运行结果
len = 8
name = 1234567
```

很明显如果目标数据正好是NAME_LEN长度，获取的结果就是错误的。

从示例代码其实可以看出来，snprintf接口第二个入参传递的是长度8,但实际结果获取的是1234567，只拷贝了7位，说明snprintf接口只会拷贝第二个入参长度-1的长度数据，最后一个字节固定存放'\0'字节。

返回的长度len是8，表明返回数据长度也是包含'\0'字节的。

查看man手册明确拷贝最长size字节（包含'\0'字节）：
> The  functions  snprintf()  and  vsnprintf() write at most size bytes (including the terminating null byte ('\0')) to
       str.


示例代码中接口说明有必要注意下，对外提供的接口一定要说清楚接口功能，各参数的含义其一些注意点。比如你所返回的数据是否是保证'\0'结尾的，也可以通过变量名类似加后缀_sz（string zero）表明会'\0'结尾。

---

示例中使用的是snprintf接口，sprintf接口与之功能一样，但不限定最大拷贝字节数，这就可能出现，组装的字符串超过目标实际长度，导致写越界，所以日常工作不推荐使用sprintf接口，而是使用snprintf。

类似的，strcpy接口应该使用strncpy替换；strcat接口应该使用strncat替换；strcmp接口应该使用strncmp替换，下面简单说下各接口平常工作关注的点。


```C
int sprintf(char *str, const char *format, ...);
int snprintf(char *str, size_t size, const char *format, ...);
```

sprintf ：没有限定目标长度，可能越界；如果目标长度足够，则保证'\0'结尾  
snprintf ：限定拷贝最大长度，**需要注意拷贝的内容一定包含'\0'字节**

<br/>

```C
char *strcpy(char *dest, const char *src);
char *strncpy(char *dest, const char *src, size_t n);
```

strcpy ：没有限定目标长度，可能拷贝越界，一定会包含'\0'字节  
strncpy ：限定目标长度，需要注意，**源数据如果大于限定长度，则目标数据是不会包含'\0'字节的**

下面是man手册中的简单实现：
```C
char *strncpy(char *dest, const char *src, size_t n)
{
	size_t i;

	for (i = 0; i < n && src[i] != '\0'; i++)
		dest[i] = src[i];
	for (; i < n; i++)
		dest[i] = '\0';

	return dest;
}
```

因为strncpy不会保证'\0'结尾，所以一般需要强制\0结尾处理，如：

```C
strncpy(buf, str, n);
if (n > 0)
    buf[n - 1]= '\0';
```

<br/>

```C
char *strcat(char *dest, const char *src);
char *strncat(char *dest, const char *src, size_t n);
```

strcat ：没有限定目标长度，可能拷贝越界，一定会包含'\0'字  
strncat ：限定目标长度，需要注意，**源数据如果大于等于限定长度，会拷贝n+1个字节到目标地址（包含'\0'字节）**

下面是man手册的简单实现：
```C
char *strncat(char *dest, const char *src, size_t n)
{
	size_t dest_len = strlen(dest);
	size_t i;

	for (i = 0; i < n && src[i] != '\0'; i++)
		dest[dest_len + i] = src[i];
	dest[dest_len + i] = '\0';

	return dest;
}
```

<br/>

```C
int strcmp(const char *s1, const char *s2);
int strncmp(const char *s1, const char *s2, size_t n);
```

strcmp ：没有限定目标长度，直到一方先到达'\0'字节，如果传入的对象不是'\0'结尾，可能出现越界访问  
strncmp ： 限定字节对比，这接口和前面的接口都不大一样，**前面的接口只需要考虑目标长度就行，这里考虑的则是哪个比较对象可能不是'\0'结尾的，针对那个非'\0'结尾的对象进行sizeof操作，注意是sizeof操作，而不是strlen操作，否则也是越界访问。如果都不明确是'\0'结尾的，那应该用memcmp，如果是明确只限定前几个字节比较就行，那就使用strncmp接口，如果都明确是'\0'结尾的，那使用strcmp就行**


