---
layout:     post
title:      strcpy-strcat-strcmp-strlen函数实现
subtitle:   库函数实现~ 
date:       2019-09-12
author:     Alpha
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - C++
---

### 一、字符串拷贝strcpy

函数`strcpy`的原型是`char* strcpy(char* des , const char* src)`，des 和 src 所指内存区域不可以重叠且 des 必须有足够的空间来容纳 src 的字符串。

```C++
#include <assert.h>
#include <stdio.h>
 
char* strcpy(char* des, const char* src)
{
	assert((des!=NULL) && (src!=NULL)); 
    
	char *address = des;  
	while((*des++ = *src++) != '\0')  
		;  
	return address;
}
```



要知道 strcpy 会拷贝'\0'，还有要注意：

- 源指针所指的字符串内容是不能修改的，因此应该声明为 const 类型。

- 要判断源指针和目的指针为空的情况，思维要严谨，这里使用assert（见文末）。

- 要用一个临时变量保存目的串的首地址，最后返回这个首地址。

- 函数返回 char* 的目的是为了支持链式表达式，即strcpy可以作为其他函数的实参。



### 二、字符串长度strlen

函数`strlen`的原型是`size_t strlen(const char *s)`，其中 size_t 就是 unsigned int。

```C++
#include <assert.h>
#include <stdio.h>
 
int strlen(const char* str)
{
	assert(str != NULL);
	int len = 0;
	while((*str++) != '\0')
		++len;
	return len;
}
```

strlen 与 sizeof 的区别：

- sizeof是运算符，strlen是库函数。

- sizeof可以用类型、变量做参数，而strlen只能用 char* 变量做参数，且必须以\0结尾。

- sizeof是在编译的时候计算类型或变量所占内存的大小，而strlen的结果要在运行的时候才能计算出来，用来计算字符串的长度。

- 数组做sizeof的参数不退化，传递给strlen就退化为指针了。



### 三、字符串连接strcat

函数`strcat`的原型是`char* strcat(char* des, char* src)`，des 和 src 所指内存区域不可以重叠且 des 必须有足够的空间来容纳 src 的字符串。

```C++
#include <assert.h>
#include <stdio.h>
 
char* strcat(char* des, const char* src)   // const表明为输入参数 
{  
	assert((des!=NULL) && (src!=NULL));
	char* address = des;
	while(*des != '\0')  // 移动到字符串末尾
		++des;
	while(*des++ = *src++)
		;
	return address;
}
```



### 四、字符串比较strcmp

函数`strcmp`的原型是`int strcmp(const char *s1,const char *s2)`。

- 若s1==s2，返回零；
- 若s1>s2，返回正数；
- 若s1<s2，返回负数。

即：两个字符串自左向右逐个字符相比（按ASCII值大小相比较），直到出现不同的字符或遇`\0`为止。

```C++
#include <assert.h>
#include <stdio.h>
 
int strcmp(const char *s1,const char *s2)
{
	assert((s1!=NULL) && (s2!=NULL));
    while(*s1 == *s2)
    {
        if(*s1 == '\0')
            return 0;
         
        ++s1;
        ++s2;
    }
    return *s1 - *s2;
}
```



### 附：assert()断言

`assert`是宏，而不是函数。它的原型定义在头文件 assert.h 中：

```
void assert( int expression );
```

宏 assert 经常用于在函数开始处检验传入参数的合法性，可以将其看作是异常处理的一种高级形式。**assert 的作用是**先计算表达式expression，然后判断：

- 如果表达式值为假，那么它先向stderr打印错误信息，然后通过调用 abort 来终止程序运行。
- 如果表达式值为真，继续运行后面的程序。

注意：`assert`只在 DEBUG 下生效，在调试结束后，可以通过在`#include <assert.h>`语句之前插入`#define NDEBUG`来禁用assert调用。

