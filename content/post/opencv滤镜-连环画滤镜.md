---
title: "opencv滤镜-连环画滤镜"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","连环画" ]
draft: false
slug: "opencv/comic-filter"
date: "2019-07-03 12:00:06"
---

## 连环画滤镜

连环画滤镜的公式为：

![连环画滤镜公式.png][1]

![filter-img](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//filter-image.jpeg)

## 代码实现

```c++
// comic-filter.cpp : 此文件包含 "main" 函数。程序执行将在此处开始并结束。
// @mango

#include<iostream>
#include<cmath>
#include<opencv2/opencv.hpp>



int main()
{

	cv::Mat img = cv::imread("fruit.jpg");

	for (size_t i = 0; i < img.rows; i++)
	{
		for (size_t j = 0; j < img.cols; j++)
		{
			int r = img.at<cv::Vec3b>(i, j)[2];
			int g = img.at<cv::Vec3b>(i, j)[1];
			int b = img.at<cv::Vec3b>(i, j)[0];

			double R = std::abs(g - b + g + r) * r / 256;
			double G = std::abs(b - g + b + r) * r / 256;
			double B = std::abs(b - g + b + r) * g / 256;

			img.at<cv::Vec3b>(i, j)[0] = cv::saturate_cast<uchar>(B);
			img.at<cv::Vec3b>(i, j)[1] = cv::saturate_cast<uchar>(G);
			img.at<cv::Vec3b>(i, j)[2] = cv::saturate_cast<uchar>(R);
		}
	}
	cv::imshow("连环画滤镜", img);

	cv::waitKey(0);
	return 0;
}
```

滤镜效果

![连环画滤镜效果.png][2]

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/opencv/comic-filter.html

  [1]: https://mangoroom.cn/usr/uploads/2019/07/720430173.png
  [2]: https://mangoroom.cn/usr/uploads/2019/07/2420844271.png