---
title: "opencv滤镜-素描"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","素描" ]
draft: false
slug: "opencv/drawing-filter"
date: "2019-07-03 17:02:00"
---

## 素描

将彩色图片转换成素描的方法有多种，本文采用的是模拟ps彩色图片转为素描图片打印的过程。算法的原理流程如下：

- 1、去色；

- 2、复制去色图层，并且反色；关于反色可以参考芒果额另一篇文章[opencv滤镜-反向滤镜](https://mangoroom.cn/opencv/reversal-filter.html)；
- 3、对反色图像进行高斯模糊；

- 4、模糊后的图像叠加模式选择颜色减淡效果。

> 减淡公式：C =MIN( A +（A×B）/（255-B）,255)，其中C为混合结果，A为去色后的像素点，B为高斯模糊后的像素点。


![原图](https://mangoroom.cn/usr/uploads/2019/07/3135764183.jpg)

## 代码实现

```c++
// drawing-filter.cpp : 素描滤镜
// @mango

#include <iostream>
#include <opencv2/opencv.hpp>
#include<cmath>

int main()
{
	cv::Mat img = cv::imread("fruit.jpg");
	if (img.empty())
	{
		std::cout << "Failed to read the image!" << std::endl;
		return -1;
	}

	//1、去色
	cv::Mat gray(img.size(), CV_8UC3);
	for (size_t i = 0; i < img.rows; i++)
	{
		for (size_t j = 0; j < img.cols; j++)
		{
			int max = std::max(
				std::max(img.at<cv::Vec3b>(i, j)[0], img.at<cv::Vec3b>(i, j)[1]),
				img.at<cv::Vec3b>(i, j)[2]
			);

			int min = std::min(
				std::min(img.at<cv::Vec3b>(i, j)[0], img.at<cv::Vec3b>(i, j)[1]),
				img.at<cv::Vec3b>(i, j)[2]
			);

			for (size_t k = 0; k < 3; k++)
			{
				gray.at<cv::Vec3b>(i, j)[k] = (max + min) / 2;
			}
		}
	}

	//2、复制去色图层，并且反色
	cv::Mat gray_revesal(img.size(), CV_8UC3);
	for (size_t i = 0; i < gray.rows; i++)
	{
		for (size_t j = 0; j < gray.cols; j++)
		{
			for (size_t k = 0; k < 3; k++)
			{
				gray_revesal.at<cv::Vec3b>(i, j)[k] = 255 - gray.at<cv::Vec3b>(i, j)[k];
			}
		}
	}

	//3、对反色图像进行高斯模糊；
	cv::GaussianBlur(gray_revesal, gray_revesal, cv::Size(7, 7), 0);

	//4、模糊后的图像叠加模式选择颜色减淡效果。
	// 减淡公式：C =MIN( A +（A×B）/（255-B）,255)，其中C为混合结果，A为去色后的像素点，B为高斯模糊后的像素点。
	cv::Mat result(gray.size(), CV_8UC3);
	for (size_t i = 0; i < gray.rows; i++)
	{
		for (size_t j = 0; j < gray.cols; j++)
		{
			for (size_t k = 0; k < 3; k++)
			{
				int a = gray.at<cv::Vec3b>(i, j)[k];
				int b = gray_revesal.at<cv::Vec3b>(i, j)[k];
				int c = std::min(a + (a * b) / (255 - b), 255);

				result.at<cv::Vec3b>(i, j)[k] = c;
			}

		}
	}

	cv::imshow("素描效果", result);
	cv::waitKey(0);

	return 0;
}
```

![素描效果.png][1]

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/opencv/drawing-filter.html

  [1]: https://mangoroom.cn/usr/uploads/2019/07/101320614.png