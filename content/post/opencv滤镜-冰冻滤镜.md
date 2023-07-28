---
title: "opencv滤镜-冰冻滤镜"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","冰冻" ]
draft: false
slug: "frozen-filter"
date: "2019-07-03 11:24:00"
---

## 冰冻滤镜

通过以下公式，对图像rgb三个分量进行调整，可以到达冰冻的滤镜特效。
![冰冻滤镜公式.png][1]

![原图](https://mangoroom.cn/usr/uploads/2019/07/3135764183.jpg)

## 代码实现

```c++
// frozen-filter.cpp : 冰冻滤镜
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
			img.at<cv::Vec3b>(i, j)[0] = cv::saturate_cast<uchar>(std::abs(img.at<cv::Vec3b>(i, j)[0] - img.at<cv::Vec3b>(i, j)[1] - img.at<cv::Vec3b>(i, j)[2]) * 3 >> 2);// blue
			img.at<cv::Vec3b>(i, j)[1] = cv::saturate_cast<uchar>(std::abs(img.at<cv::Vec3b>(i, j)[1] - img.at<cv::Vec3b>(i, j)[0] - img.at<cv::Vec3b>(i, j)[2]) * 3 >> 2);// green
			img.at<cv::Vec3b>(i, j)[2] = cv::saturate_cast<uchar>(std::abs(img.at<cv::Vec3b>(i, j)[2] - img.at<cv::Vec3b>(i, j)[0] - img.at<cv::Vec3b>(i, j)[1]) * 3 >> 2);// red
		}
	}
	cv::imshow("冰冻滤镜", img);

	cv::waitKey(0);
	return 0;
}

```

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://mangoroom.cn/opencv/frozen-filter.html

![冰冻滤镜效果.png][2]


  [1]: https://mangoroom.cn/usr/uploads/2019/07/1555380516.png
  [2]: https://mangoroom.cn/usr/uploads/2019/07/1683036734.png