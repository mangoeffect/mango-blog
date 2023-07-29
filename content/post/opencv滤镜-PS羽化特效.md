---
title: "opencv滤镜-PS羽化特效"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","羽化","PS" ]
draft: false
slug: "opencv/feather-effect-filter"
date: "2019-07-04 23:24:00"
---

## 羽化特效

> 羽化是ps术语，羽化原理是令选区内外衔接部分虚化，起到渐变的作用从而达到自然衔接的效果，是ps及其其它版本中的处理图片的重要工具。羽化可使像素选区的边缘变得模糊，有助于所选区域与周围的像素混合.


![原图](https://mangoroom.cn/usr/uploads/2019/07/3135764183.jpg)

## 实现原理

羽化值越大，朦胧范围越宽，羽化值越小，朦胧范围越窄。可根据你想留下图的大小来调节。
算法分析：
- 1、通过对rgb值增加额外的V值实现朦胧效果
- 2、通过控制V值的大小实现范围控制。
- 3、V  = 255 * 当前点Point距中点距离的平方s1 / (顶点距中点的距离平方 *mSize)s2;
- 4、s1 有根据 ratio 修正 dx dy值。


## 代码实现

```c++
// feather-filter.cpp : 羽化
// @mango
// https://mangoroom.cn

#include <iostream>
#include<cmath>
#include<opencv2/opencv.hpp>



int main()
{
	//1、通过对rgb值增加额外的V值实现朦胧效果
	//2、通过控制V值的大小实现范围控制。
	//3、V = 255 * 当前点Point距中点距离的平方s1 / (顶点距中点的距离平方s2 * mSize);
	//4、s1 有根据 ratio 修正 dx dy值。

	cv::Mat img = cv::imread("fruit.jpg");

	if (img.empty())
	{
		std::cout << "Failed to read the image!" << std::endl;
		return -1;
	}

	// s2
	int center_x = img.cols >> 1;
	int center_y = img.rows >> 1;
	int s2 = center_x * center_x + center_y * center_y;

	// 宽长比例 ratio
	double ratio = img.cols > img.rows ? static_cast<double>(img.rows) / img.cols : static_cast<double>(img.cols) / img.rows;

	// mSize
	// 2、通过控制V值的大小实现范围控制。
	double mSize = 0.5;

	for (size_t i = 0; i < img.rows; i++)
	{
		for (size_t j = 0; j < img.cols; j++)
		{
			double dx = static_cast<double>(std::abs(center_x - static_cast<int>(j)));
			double dy = static_cast<double>(std::abs(center_y - static_cast<int>(i)));


			//4、s1 有根据 ratio 修正 dx dy值。
			if (center_x > center_y)
			{
				dx = dx * ratio;
			}
			else
			{
				dy = dx * ratio;
			}
			
			// s1
			double s1 = dx * dx + dy * dy;
			// v
			// 3、V = 255 * 当前点Point距中点距离的平方s1 / (顶点距中点的距离平方s2 * mSize);
			double v = 255 * s1 / (s2 * mSize);

			int b = img.at<cv::Vec3b>(i, j)[0];
			int g = img.at<cv::Vec3b>(i, j)[1];
			int r = img.at<cv::Vec3b>(i, j)[2];

			// 1、通过对rgb值增加额外的V值实现朦胧效果
			img.at<cv::Vec3b>(i, j)[0] = cv::saturate_cast<uchar>(b + v);
			img.at<cv::Vec3b>(i, j)[1] = cv::saturate_cast<uchar>(g + v);
			img.at<cv::Vec3b>(i, j)[2] = cv::saturate_cast<uchar>(r + v);
		}
	}

	cv::imshow("羽化特效", img);
	cv::waitKey(0);

	return 0;
}



```

![羽化特效.png][1]

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/opencv/feather-effect-filter.html


  [1]: https://mangoroom.cn/usr/uploads/2019/07/2531801425.png