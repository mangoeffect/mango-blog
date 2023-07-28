---
title: "opencv滤镜-熔铸滤镜"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","熔铸" ]
draft: false
slug: "casting-filter"
date: "2019-07-03 10:59:00"
---

## 熔铸滤镜

与怀旧滤镜类似，通过对图像rgb三个分量的调整变化，可以得到熔铸滤镜的效果。以下是调整的公式：

![熔铸滤镜公式.png][1]

![原图](https://mangoroom.cn/usr/uploads/2019/07/3135764183.jpg)

## 代码实现

```c++
// casting-filter.cpp : 熔铸滤镜
// @mango

#include<iostream>
#include<opencv2/opencv.hpp>



int main()
{

	cv::Mat img = cv::imread("fruit.jpg");

	for (size_t i = 0; i < img.rows; i++)
	{
		for (size_t j = 0; j < img.cols; j++)
		{
			img.at<cv::Vec3b>(i, j)[0] = cv::saturate_cast<uchar>(128 * img.at<cv::Vec3b>(i, j)[0] /( img.at<cv::Vec3b>(i, j)[1] + img.at<cv::Vec3b>(i, j)[2] + 1));// blue
			img.at<cv::Vec3b>(i, j)[1] = cv::saturate_cast<uchar>(128 * img.at<cv::Vec3b>(i, j)[1] /( img.at<cv::Vec3b>(i, j)[0] + img.at<cv::Vec3b>(i, j)[2] + 1));// green
			img.at<cv::Vec3b>(i, j)[2] = cv::saturate_cast<uchar>(128 * img.at<cv::Vec3b>(i, j)[2] /( img.at<cv::Vec3b>(i, j)[0] + img.at<cv::Vec3b>(i, j)[1] + 1));// red
		}
	}
	cv::imshow("熔铸滤镜", img);

	cv::waitKey(0);
	return 0;
}
```

滤镜效果

![熔铸滤镜效果.png][2]

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://mangoroom.cn/opencv/casting-filter.html

  [1]: https://mangoroom.cn/usr/uploads/2019/07/3401248396.png
  [2]: https://mangoroom.cn/usr/uploads/2019/07/3407989805.png