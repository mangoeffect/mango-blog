---
title: "opencv坐标点模板类Point_用法"
categories: [ "opencv","c++","计算机视觉" ]
tags: [ "opencv","point" ]
draft: false
slug: "how-to-use-point-type-in-opencv"
date: "2020-09-30 21:15:00"
---

有时候我们在实现一些图像处理算法的时候，往往会对图像像素点进行相关的运算和处理。传入或输出的坐标点往往是x,y的数组向量，如

```cpp
//int 类型
std::vector<cv::Point2i> pts;
std::vector<cv::Point> pts;

//float类型
std::vector<cv::Point2f> pts

//double 类型
std::vector<cv::Point2d> pts
```
opencv中内置了三种二维平面点坐标类型，这就带来一个问题：即我们在编写图像处理算法的时候有时候并不确定调用者需要哪一种坐标点数据类型，只写一种吧可能无法满足需要，三种都分别实现又费时耗力，且没利用好c++多态泛型的特性。而opencv中的模板类```cv::Point_```刚好可以解决这个问题，实际上以上三种类型的点都是```cv::Point_```的具体实例化，在opencv里转到定义就可以查看到

```cpp
typedef Point_<int> Point2i;
typedef Point_<int64> Point2l;
typedef Point_<float> Point2f;
typedef Point_<double> Point2d;
typedef Point2i Point;
```
所以实际上内置的三种类型点都是模板类的具体实例化，参考其代码理论上你还可以自己扩展新的点集类型，比如

```cpp
typedef Point_<short> Point2s;
typedef Point_<long int> Point2l;
//...
```
在opencv里面可以查看到```cv::Point_```的定义是这样子的：

```cpp
template<typename _Tp> class Point_
{
public:
    typedef _Tp value_type;

    //! default constructor
    Point_();
    Point_(_Tp _x, _Tp _y);
    Point_(const Point_& pt);
    Point_(const Size_<_Tp>& sz);
    Point_(const Vec<_Tp, 2>& v);

    Point_& operator = (const Point_& pt);
    //! conversion to another data type
    template<typename _Tp2> operator Point_<_Tp2>() const;

    //! conversion to the old-style C structures
    operator Vec<_Tp, 2>() const;

    //! dot product
    _Tp dot(const Point_& pt) const;
    //! dot product computed in double-precision arithmetics
    double ddot(const Point_& pt) const;
    //! cross-product
    double cross(const Point_& pt) const;
    //! checks whether the point is inside the specified rectangle
    bool inside(const Rect_<_Tp>& r) const;
    _Tp x; //!< x coordinate of the point
    _Tp y; //!< y coordinate of the point
};
```
比较简洁，里面就定义了构造函数、花括号列表初始化、点乘以及叉乘等操作。回到最开始的问题，如何利用模板类```cv::Point_```一次编写适用多种类型坐标点的算法呢。原理很简单，其实就是c++模板的用法。下面是一个最简单的c++使用方法

```cpp
template <typename T>
inline T const& max (T const& a, T const& b) 
{ 
    return a < b ? b:a; 
} 
```
上面例子中即用一个不确定的类型T做形参的类型声明，让编译器在编译的时候实例化成哪种基本类型，而我们的点坐标数据类型不是基本类型自然不直接用T,我们泛化的点数据类型应为

```cpp
template<typename T>
cv::Point_<T>
```
所以，我们的函数形参可以如下定义，以计算点集重心为例

```cpp
template<typename T>
int ComputeCentroid(const std::vector<cv::Point_<T>>& points, cv::Point_<T>& centroid);
```

函数的实现如下

```cpp
template<typename T>
int ComputeCentroid(const std::vector<cv::Point_<T>>& points, cv::Point_<T>& centroid)
{
	if (points.empty())
		return -1;
	T x = 0;
	T y = 0;

	for (auto& pt : points)
	{
		x += pt.x;
		y += pt.y;
	}
    
	centroid.x = x / points.size();
	centroid.y = y / points.size();

	return 0;
}
```
接下来测试各种类型的点数据类型

- int类型：

```cpp
//int类型点集测试
std::vector<cv::Point> points_int;
for (int i = 0; i < 10; ++i)
{
    points_int.push_back({ i, i });
}
cv::Point ct_int;
ComputeCentroid(points_int, ct_int);

std::cout << "centroid: " << ct_int << std::endl;
```


测试结果输出：

```
centroid: [4, 4]
```

- float类型：

```cpp
//float类型点集测试
std::vector<cv::Point2f> points_float;
for (int i = 0; i < 10; ++i)
{
    float fi = static_cast<float>(i);
    points_float.push_back({ fi, fi });
}
cv::Point2f ct_float;
ComputeCentroid(points_float, ct_float);

std::cout << "centroid: " << ct_float << std::endl;
```


测试结果输出：

```
centroid: [4.5, 4.5]
```

- double类型：

```cpp
//double类型点集测试
std::vector<cv::Point2d> points_double;
for (int i = 0; i < 10; ++i)
{
    double di = static_cast<double>(i);
    points_double.push_back({ di, di });
}
cv::Point2d ct_double;
ComputeCentroid(points_double, ct_double);

std::cout << "centroid: " << ct_double << std::endl;
```


测试结果输出：

```
centroid: [4.5, 4.5]
```

完整代码如下:

```cpp
#include <iostream>
#include <vector>

#include "opencv2/opencv.hpp"

template<typename T>
int ComputeCentroid(const std::vector<cv::Point_<T>>& points, cv::Point_<T>& centroid);

int main()
{
	//int类型点集测试
	std::vector<cv::Point> points_int;
	for (int i = 0; i < 10; ++i)
	{
		points_int.push_back({ i, i });
	}
	cv::Point ct_int;
	ComputeCentroid(points_int, ct_int);

	std::cout << "centroid: " << ct_int << std::endl;

	//float类型点集测试
	std::vector<cv::Point2f> points_float;
	for (int i = 0; i < 10; ++i)
	{
		float fi = static_cast<float>(i);
		points_float.push_back({ fi, fi });
	}
	cv::Point2f ct_float;
	ComputeCentroid(points_float, ct_float);
	
	std::cout << "centroid: " << ct_float << std::endl;

	//double类型点集测试
	std::vector<cv::Point2d> points_double;
	for (int i = 0; i < 10; ++i)
	{
		double di = static_cast<double>(i);
		points_double.push_back({ di, di });
	}
	cv::Point2d ct_double;
	ComputeCentroid(points_double, ct_double);

	std::cout << "centroid: " << ct_double << std::endl;
	return 0;
}

template<typename T>
int ComputeCentroid(const std::vector<cv::Point_<T>>& points, cv::Point_<T>& centroid)
{
	if (points.empty())
		return -1;
	T x = 0;
	T y = 0;

	for (auto& pt : points)
	{
		x += pt.x;
		y += pt.y;
	}
    
	centroid.x = x / points.size();
	centroid.y = y / points.size();

	return 0;
}
```

测试结果输出：

```
centroid: [4, 4]
centroid: [4.5, 4.5]
centroid: [4.5, 4.5]
```

-----------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/opencv/how-to-use-point-type-in-opencv.html

