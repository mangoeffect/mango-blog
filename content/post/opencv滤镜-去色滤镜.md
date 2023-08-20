---
title: "opencv滤镜-去色滤镜"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","去色" ]
draft: false
slug: "opencv/remove-color-filter"
date: "2019-07-03 09:32:00"
---

## 去色滤镜

去色滤镜与灰度化的效果比较相近，对图像的处理也都是比较相近。去色处理之后的图像实际也是一张灰度图（三个通道分量灰度值相等，与灰度图一个通道的显示效果一致）。去色的原理是，分别取r、g、b三个分量的最大值与最小值，然后将最大值与最小值取平均值作为新的灰度值。

![filter-img](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//filter-image.jpeg)

## 代码实现

```c++
// remove-color.cpp 
// @mango

#include <iostream>
#include<cmath>
#include<opencv2/opencv.hpp>



int main()
{
	cv::Mat img = cv::imread("fruit.jpg");
	if (img.empty())
	{
		std::cout << "Failed to read the  image!" << std::endl;
		return -1;
	}

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
				img.at<cv::Vec3b>(i, j)[k] = (max + min) / 2;
			}
			
		}
	}

	cv::imshow("去色滤镜", img);
	cv::waitKey(0);

	return 0;
}

```
去色效果

![去色滤镜效果.png][1]

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/opencv/remove-color-filter.html

  [1]: https://mangoroom.cn/usr/uploads/2019/07/3241590881.png