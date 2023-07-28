---
title: "使用lambda表达式对自定义对象数组进行排序"
categories: [ "c++" ]
tags: [ "c++","lambda","sort" ]
draft: false
slug: "use-lambda-to-sort-custom-object-array"
date: "2020-06-12 15:49:00"
---

在C++11以前，对自定义对象的数组排序是一个比较麻烦的事情，需要自行编写定制的排序算法，或者使用函数绑定传入到sort函数里面。这么做比较麻烦，针对自定义对象的每一个属性做排序条件都要写一遍，在C++11出来后，匿名函数lambda表达式就解决了这个问题。

![images.png][1]

- 自定义对象
```cpp
class MyClass
{
public:
	MyClass(const int& t_intProperty, const char& t_charProperty) 
		: m_IntProperty(t_intProperty), m_CharPorerty(t_charProperty){};

	int m_IntProperty;
	char m_CharPorerty;
private:
	
};
```
- 简单的向量测试

```cpp
void SimpleVectorTest()
{
	std::cout << "Simple vector test:" << std::endl;

	std::vector<int> iVec = { 1, 5, 3, 2, 7, 0, 6, 4, 8, 9, 9 };
	std::sort(iVec.begin(), iVec.end(), [](const int& a, const int& b) { return a < b; });
	for (const auto& e : iVec)
	{
		std::cout << e << " ";
	}

	std::cout << std::endl;
	std::vector<char> cVec = { 'b', 'e', 'f', 'a', 'd', 'c' };
	std::sort(cVec.begin(), cVec.end(), [](const char& a, const char& b) {return a < b; });
	for (const auto& e : cVec)
	{
		std::cout << e << " ";
	}
	std::cout << std::endl;
}
```

输出

```
// out put
/*
Simple vector test:
0 1 2 3 4 5 6 7 8 9 9
a b c d e f
*/
```
- 简单数组测试

```cpp
void SimpleArrayTest()
{
	std::cout << "Simple array test:" << std::endl;

	std::array<int, 11> iArr = { 1, 5, 3, 2, 7, 0, 6, 4, 8, 9, 9 };

	std::sort(iArr.begin(), iArr.end(), [](const int& a, const int& b) { return a < b; });

	for (const auto& e : iArr)
	{
		std::cout << e << " ";
	}

	std::cout << std::endl;
	std::array<char, 6> cArr = { 'b', 'e', 'f', 'a', 'd', 'c' };

	std::sort(cArr.begin(), cArr.end(), [](const char& a, const char& b) {return a < b; });

	for (const auto& e : cArr)
	{
		std::cout << e << " ";
	}
	std::cout << std::endl;
}
```

输出

```
// out put
Simple array test:
0 1 2 3 4 5 6 7 8 9 9
a b c d e f
```

- 自定义对象向量测试

```cpp
void CustomObjectVectorTest()
{
	std::cout << "Custom object vector test:" << std::endl;
	std::vector<MyClass> objVec = { {3, 'c'} ,{1 , 'a'}, {4, 'd'} , {2, 'b'},  {5, 'f'} };

	std::sort(objVec.begin(), objVec.end(), [](const MyClass& a, const MyClass& b) {return a.m_IntProperty < b.m_IntProperty; });

	std::cout << "Vector sort by int type property:";
	for (const auto& e : objVec)
	{
		std::cout << "[" << e.m_IntProperty << "," << e.m_CharPorerty << "]\t";
	}

	std::cout << std::endl;

	std::sort(objVec.begin(), objVec.end(), [](const MyClass& a, const MyClass& b) {return a.m_CharPorerty < b.m_CharPorerty; });

	std::cout << "Vector sort by char type property:";
	for (const auto& e : objVec)
	{
		std::cout << "[" << e.m_IntProperty << "," << e.m_CharPorerty << "]\t";
	}

	std::cout << std::endl;	
}
```

输出

```
Custom object vector test:
Vector sort by int type property:[1,a]  [2,b]   [3,c]   [4,d]   [5,f]
Vector sort by char type property:[1,a] [2,b]   [3,c]   [4,d]   [5,f]
```

- 自定义对象数组测试

```cpp
void CustomObjectArrayTest()
{
	std::cout << "Custom object array test:" << std::endl;
	// !!!!----not work--------!!!!
	//std::array<MyClass, 5> objArr = { {3, 'c'} ,{1 , 'a'}, {4, 'd'} , {2, 'b'},  {5, 'f'} };
	// !!!!----not work--------!!!!
	std::array<MyClass, 5> objArr = { MyClass{3, 'c'} ,{1 , 'a'}, {4, 'd'} , {2, 'b'}, {5, 'f'} };

	std::sort(objArr.begin(), objArr.end(), [](const MyClass& a, const MyClass& b) {return a.m_IntProperty < b.m_IntProperty; });

	std::cout << "Array sort by int type property:";
	for (const auto& e : objArr)
	{
		std::cout << "[" << e.m_IntProperty << "," << e.m_CharPorerty << "]\t";
	}
	std::cout << std::endl;

	std::sort(objArr.begin(), objArr.end(), [](const MyClass& a, const MyClass& b) {return a.m_CharPorerty < b.m_CharPorerty; });

	std::cout << "Array sort by char type property:";
	for (const auto& e : objArr)
	{
		std::cout << "[" << e.m_IntProperty << "," << e.m_CharPorerty << "]\t";
	}

	std::cout << std::endl;
}
```

输出

```
Custom object array test:
Array sort by int type property:[1,a]   [2,b]   [3,c]   [4,d]   [5,f]
Array sort by char type property:[1,a]  [2,b]   [3,c]   [4,d]   [5,f]
```

- c风格数组测试

```cpp
void CstyleArrayTest()
{
	std::cout << "C-style array test:" << std::endl;

	int iArr[11] = { 1, 5, 3, 2, 7, 0, 6, 4, 8, 9, 9 };

	std::sort(std::begin(iArr), std::end(iArr), [](const int& a, const int& b) { return a < b; });

	for (const auto& e : iArr)
	{
		std::cout << e << " ";
	}

	std::cout << std::endl;
	char cArr[6] = { 'b', 'e', 'f', 'a', 'd', 'c' };
	std::sort(std::begin(cArr), std::end(cArr), [](const char& a, const char& b) {return a < b; });
	for (const auto& e : cArr)
	{
		std::cout << e << " ";
	}
	std::cout << std::endl;
}
```

输出

```
C-style array test:
0 1 2 3 4 5 6 7 8 9 9
a b c d e f
```

- 完整代码

```cpp
// std::sort with lambda function

#include<iostream>
#include<cstring>
#include<vector>
#include<algorithm>
#include<array>


class MyClass
{
public:
	MyClass(const int& t_intProperty, const char& t_charProperty) 
		: m_IntProperty(t_intProperty), m_CharPorerty(t_charProperty){};

	int m_IntProperty;
	char m_CharPorerty;
private:
	
};


void SimpleVectorTest()
{
	std::cout << "Simple vector test:" << std::endl;

	std::vector<int> iVec = { 1, 5, 3, 2, 7, 0, 6, 4, 8, 9, 9 };
	std::sort(iVec.begin(), iVec.end(), [](const int& a, const int& b) { return a < b; });
	for (const auto& e : iVec)
	{
		std::cout << e << " ";
	}

	std::cout << std::endl;
	std::vector<char> cVec = { 'b', 'e', 'f', 'a', 'd', 'c' };
	std::sort(cVec.begin(), cVec.end(), [](const char& a, const char& b) {return a < b; });
	for (const auto& e : cVec)
	{
		std::cout << e << " ";
	}
	std::cout << std::endl;
}
// out put
/*
Simple vector test:
0 1 2 3 4 5 6 7 8 9 9
a b c d e f
*/

void SimpleArrayTest()
{
	std::cout << "Simple array test:" << std::endl;

	std::array<int, 11> iArr = { 1, 5, 3, 2, 7, 0, 6, 4, 8, 9, 9 };

	std::sort(iArr.begin(), iArr.end(), [](const int& a, const int& b) { return a < b; });

	for (const auto& e : iArr)
	{
		std::cout << e << " ";
	}

	std::cout << std::endl;
	std::array<char, 6> cArr = { 'b', 'e', 'f', 'a', 'd', 'c' };

	std::sort(cArr.begin(), cArr.end(), [](const char& a, const char& b) {return a < b; });

	for (const auto& e : cArr)
	{
		std::cout << e << " ";
	}
	std::cout << std::endl;
}
// out put
/*
Simple array test:
0 1 2 3 4 5 6 7 8 9 9
a b c d e f
*/

void CustomObjectVectorTest()
{
	std::cout << "Custom object vector test:" << std::endl;
	std::vector<MyClass> objVec = { {3, 'c'} ,{1 , 'a'}, {4, 'd'} , {2, 'b'},  {5, 'f'} };

	std::sort(objVec.begin(), objVec.end(), [](const MyClass& a, const MyClass& b) {return a.m_IntProperty < b.m_IntProperty; });

	std::cout << "Vector sort by int type property:";
	for (const auto& e : objVec)
	{
		std::cout << "[" << e.m_IntProperty << "," << e.m_CharPorerty << "]\t";
	}

	std::cout << std::endl;

	std::sort(objVec.begin(), objVec.end(), [](const MyClass& a, const MyClass& b) {return a.m_CharPorerty < b.m_CharPorerty; });

	std::cout << "Vector sort by char type property:";
	for (const auto& e : objVec)
	{
		std::cout << "[" << e.m_IntProperty << "," << e.m_CharPorerty << "]\t";
	}

	std::cout << std::endl;	
}

//out put
/*
Custom object vector test:
Vector sort by int type property:[1,a]  [2,b]   [3,c]   [4,d]   [5,f]
Vector sort by char type property:[1,a] [2,b]   [3,c]   [4,d]   [5,f]
*/

void CustomObjectArrayTest()
{
	std::cout << "Custom object array test:" << std::endl;
	// !!!!----not work--------!!!!
	//std::array<MyClass, 5> objArr = { {3, 'c'} ,{1 , 'a'}, {4, 'd'} , {2, 'b'},  {5, 'f'} };
	// !!!!----not work--------!!!!
	std::array<MyClass, 5> objArr = { MyClass{3, 'c'} ,{1 , 'a'}, {4, 'd'} , {2, 'b'}, {5, 'f'} };

	std::sort(objArr.begin(), objArr.end(), [](const MyClass& a, const MyClass& b) {return a.m_IntProperty < b.m_IntProperty; });

	std::cout << "Array sort by int type property:";
	for (const auto& e : objArr)
	{
		std::cout << "[" << e.m_IntProperty << "," << e.m_CharPorerty << "]\t";
	}
	std::cout << std::endl;

	std::sort(objArr.begin(), objArr.end(), [](const MyClass& a, const MyClass& b) {return a.m_CharPorerty < b.m_CharPorerty; });

	std::cout << "Array sort by char type property:";
	for (const auto& e : objArr)
	{
		std::cout << "[" << e.m_IntProperty << "," << e.m_CharPorerty << "]\t";
	}

	std::cout << std::endl;
}

//out put
/*
Custom object array test:
Array sort by int type property:[1,a]   [2,b]   [3,c]   [4,d]   [5,f]
Array sort by char type property:[1,a]  [2,b]   [3,c]   [4,d]   [5,f]
*/

void CstyleArrayTest()
{
	std::cout << "C-style array test:" << std::endl;

	int iArr[11] = { 1, 5, 3, 2, 7, 0, 6, 4, 8, 9, 9 };

	std::sort(std::begin(iArr), std::end(iArr), [](const int& a, const int& b) { return a < b; });

	for (const auto& e : iArr)
	{
		std::cout << e << " ";
	}

	std::cout << std::endl;
	char cArr[6] = { 'b', 'e', 'f', 'a', 'd', 'c' };
	std::sort(std::begin(cArr), std::end(cArr), [](const char& a, const char& b) {return a < b; });
	for (const auto& e : cArr)
	{
		std::cout << e << " ";
	}
	std::cout << std::endl;
}

//out put
/*
C-style array test:
0 1 2 3 4 5 6 7 8 9 9
a b c d e f
*/

int main()
{
	SimpleVectorTest();
	
	SimpleArrayTest();

	CustomObjectVectorTest();

	CustomObjectArrayTest();

	CstyleArrayTest();

	return 0;
}
```

-----

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/cpp/use-lambda-to-sort-custom-object-array.html

  [1]: https://mangoroom.cn/usr/uploads/2020/06/1197349793.png