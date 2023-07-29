---
title: "c++整数转换为字符串"
categories: [ "c++" ]
tags: [ "c++","字符串" ]
draft: false
slug: "cpp/some-ways-to-convert-int-to-string-in-cpp"
date: "2020-01-20 15:40:00"
---

c++与c#不同，c#对面向对象的支持做的比较全面，在c#里面每一个变量都是一个对象，可以方便地调用ToString()方法将对象转换为字符串。但c++却没法这么方便地转换，需要利用一些方法转换。

## (1)利用itoa函数

itoa函数原型

```c++
char *  itoa ( int value, char * str, int base );
```

使用指定的基数将整数值转换为以null终止的字符串，并将结果存储在str参数给定的数组中。

使用示例

```c++
/* itoa example */
#include <stdio.h>
#include <stdlib.h>

int main ()
{
  int i;
  char buffer [33];
  printf ("Enter a number: ");
  scanf ("%d",&i);
  itoa (i,buffer,10);
  printf ("decimal: %s\n",buffer);
  itoa (i,buffer,16);
  printf ("hexadecimal: %s\n",buffer);
  itoa (i,buffer,2);
  printf ("binary: %s\n",buffer);
  return 0;
}
```

或者

```c++
int a = 10;
char *intStr = itoa(a);
string str = string(intStr);
```

## (2)使用stringstream 

```c++
int a = 10;
stringstream ss;
ss << a;
string str = ss.str();
```

## (3)使用to_string()函数

```c++
#include <string> 

std::string s = std::to_string(42);
```
或使用auto关键字

```c++
#include <string> 

auto s = std::to_string(42);
```

## references

[【1】C++ reference](http://www.cplusplus.com/reference/cstdlib/itoa/)
[【2】Stackoverflow](https://stackoverflow.com/questions/5590381/easiest-way-to-convert-int-to-string-in-c)

---

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/cpp/some-ways-to-convert-int-to-string-in-cpp.html