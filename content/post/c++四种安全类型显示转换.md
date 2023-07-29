---
title: "c++四种安全类型显示转换"
categories: [ "c++" ]
tags: [ "c++","c","编程语言","cast" ]
draft: false
slug: "cpp/cpp-four-types-cast"
date: "2019-06-16 10:13:00"
---

## c++四种安全类型显示转换



### static_cast



主要用于c++中内置的基本数据类型之间的转换 ,这种强制转换只会在编译时检查。 如果编译器检测到您尝试强制转换完全不兼容的类型，则static_cast会返回错误。 您还可以使用它在基类指针和派生类指针之间强制转换，但是，编译器在无法分辨此类转换在运行时是否是安全的。 

主要用法：

- 用于类层次结构中基类（父类）和[派生类](http://baike.sogou.com/lemma/ShowInnerLink.htm?lemmaId=3825458)（子类）之间指针或引用的转换。

  进行上行转换（把派生类的指针或引用转换成基类表示）是安全的；

  进行下行转换（把基类指针或引用转换成派生类表示）时，由于没有动态类型检查，所以是不安全的。

- 用于基本数据类型之间的转换，如把int转换成[char](http://baike.sogou.com/lemma/ShowInnerLink.htm?lemmaId=4836436)，把int转换成enum。这种转换的安全性也要开发人员来保证。 

- 把空指针转换成目标类型的空指针。 

- 把任何类型的表达式转换成[void](http://baike.sogou.com/lemma/ShowInnerLink.htm?lemmaId=659573)类型。

```c++
double d = 1.58947;  
int i = d;  // warning C4244 possible loss of data  
int j = static_cast<int>(d);       // No warning.  
string s = static_cast<string>(d); // Error C2440:cannot convert from  
                                   // double to std:string  

// No error but not necessarily safe.  
Base* b = new Base();  
Derived* d2 = static_cast<Derived*>(b);  


int i = 0x7fff;  
long l;  
float f;  
char c;  
// （1）典型的非强制转换（自动转换）  
// 传统方式：  
l = i;  
f = i;  
// 提倡的新方式：  
l = static_cast<long>(i);  
f = static_cast<float>(i);  
// （2）窄化转换  
// 传统方式：  
// 会显示警告信息：  
i = l; // 可能丢失数字  
i = f; // 可能丢失信息  
c = i; // 可能丢失数字  
// 不显示警告信息（但仍然难定位）：  
i = (int)l;  
i = (int)f;  
c = (char)i;  
// 提倡的新方式（不会显示警告信息，且易定位）：  
i = static_cast<int>(l);  
i = static_cast<int>(f);  
c = static_cast<char>(i);  
```



### dynamic_cast



为了安全，`dynamic_cast`在运行时检查基类指针和派生类指针之间的强制转换。 `dynamic_cast` 是比 `static_cast` 更安全的强制类型转换，但运行时检查会带来一些开销。 



- dynamic_cast主要用于类层次间的上行转换和下行转换，还可以用于类之间的交叉转换。 



```c++
Base* b = new Base();  

// Run-time check to determine whether b is actually a Derived*  
Derived* d3 = dynamic_cast<Derived*>(b);  

// If b was originally a Derived*, then d3 is a valid pointer.  
if(d3)  
{  
   // Safe to call Derived method.  
   cout << d3->DoSomethingMore() << endl;  
}  
else  
{  
   // Run-time check failed.  
   cout << "d3 is null" << endl;  
}  

//Output: d3 is null;  
```

### const_cast

const_cast，用于修改类型的const或volatile属性。 const_cast<type_id> (expression) 

主要用法

- 常量指针被转化成非常量的指针，并且仍然指向原来的对象； 
- 常量引用被转换成非常量的引用，并且仍然指向原来的对象； 
- const_cast一般用于修改底指针。如const char *p形式。 

```c++
//常量指针被转化成非常量的指针，并且仍然指向原来的对象；
const int i = 0;  
int *pi;  
pi = &i; // 错误  
pi = (int *)&i; // 被反对  
pi = const_cast<int *>(&i); // 完美 

//常量引用被转换成非常量的引用，并且仍然指向原来的对象； 
void Func(double& d) { ... }  
void ConstCast()  
{  
   const double pi = 3.14;  
   Func(const_cast<double&>(pi)); //No error.  
}  
```



### reinterpret_cast

```c++
reinterpret_cast<type-id> (expression)
```

type-id 必须是一个指针、引用、算术类型、函数指针或者成员指针。它可以把一个指针转换成一个整数，也可以把一个整数转换成一个指针（先把一个指针转换成一个整数，再把该整数转换成原类型的指针，还可以得到原先的指针值）。 

```c++
//reinterpret_cast 和 static_cast 之间的差异。
const char* str = "hello";  
int i = static_cast<int>(str);//error C2440: 'static_cast' : cannot  
                              // convert from 'const char *' to 'int'  
int j = (int)str; // C-style cast. Did the programmer really intend  
                  // to do this?  
int k = reinterpret_cast<int>(str);// Programming intent is clear.  
                                   // However, it is not 64-bit safe.  
```



参考：

1. [c++有哪四个类型转换相关的关键字](https://blog.csdn.net/weibo1230123/article/details/78713427)

2. [类型转换和类型安全（现代 C++）](https://msdn.microsoft.com/zh-cn/library/hh279667.aspx)

    

本文由芒果浩明发布，转载需注明来源。
本文链接：https://blog.mangoroom.cn/cpp/cpp-four-types-cast.html