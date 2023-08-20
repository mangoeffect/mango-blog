---
title: "opencv滤镜-PS扩散特效"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","PS","扩散" ]
draft: false
slug: "opencv/ps-diffusion-effect-filter"
date: "2019-07-05 14:07:22"
---

## PS扩散特效

扩散特效是PS里的一种特效，经过扩散特效处理的图像会有一种类似毛玻璃的模糊效果，所以也被成为毛玻璃特效。一种简单的实现原理为，随机抽取当前像素点周围邻近的像素点的灰度值来代替当前点。如：

```
1 2 3
4 p 5
6 7 8
```

p的取值从相邻的8个像素点获取。


![filter-img](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//filter-image.jpeg)


## 代码实现

```c++
// ps-diffusion-filter.cpp : ps扩散特效
// @mango
// https://mangoroom.cn

#include <iostream>
#include<random>
#include<opencv2/opencv.hpp>


int main()
{
	cv::Mat img = cv::imread("fruit.jpg");
	if (img.empty())
	{
		std::cout << "failed to read the image!" << std::endl;
		return -1;
	}

	// random engine | 随机数引擎
	std::default_random_engine generator;
	std::uniform_int_distribution<int> dis(1, 8);

	// result 
	cv::Mat result(img.size(), CV_8UC3);
	
	for (size_t i = 1; i < img.rows - 1; i++)
	{
		for (size_t j = 1; j < img.cols - 1; j++)
		{
			// generator random | 产生随机数
			int r = dis(generator);
			// 1 2 3
			// 4 p 5
			// 6 7 8
			switch (r)
			{
			case 1:
				for (size_t k = 0; k < 3; k++)
				{
					result.at<cv::Vec3b>(i, j)[k] = img.at<cv::Vec3b>(i - 1, j - 1)[k];
				}
				break;
			case 2:
				for (size_t k = 0; k < 3; k++)
				{
					result.at<cv::Vec3b>(i, j)[k] = img.at<cv::Vec3b>(i - 1, j)[k];
				}
				break;
			case 3:
				for (size_t k = 0; k < 3; k++)
				{
					result.at<cv::Vec3b>(i, j)[k] = img.at<cv::Vec3b>(i - 1, j + 1)[k];
				}
				break;
			case 4:
				for (size_t k = 0; k < 3; k++)
				{
					result.at<cv::Vec3b>(i, j)[k] = img.at<cv::Vec3b>(i, j - 1)[k];
				}
				break;
			case 5:
				for (size_t k = 0; k < 3; k++)
				{
					result.at<cv::Vec3b>(i, j)[k] = img.at<cv::Vec3b>(i, j + 1)[k];
				}
				break;
			case 6:
				for (size_t k = 0; k < 3; k++)
				{
					result.at<cv::Vec3b>(i, j)[k] = img.at<cv::Vec3b>(i + 1, j - 1)[k];
				}
				break;
			case 7:
				for (size_t k = 0; k < 3; k++)
				{
					result.at<cv::Vec3b>(i, j)[k] = img.at<cv::Vec3b>(i + 1, j)[k];
				}
				break;
			case 8:
				for (size_t k = 0; k < 3; k++)
				{
					result.at<cv::Vec3b>(i, j)[k] = img.at<cv::Vec3b>(i + 1, j + 1)[k];
				}
				break;
			default:
				assert(false);
				break;
			}
		}
	}

	cv::imshow("PS扩散特效", result);
	cv::waitKey(0);

	return 0;
}
```
![PS扩散特效.png][1]

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/opencv/ps-diffusion-effect-filter.html


  


  [1]: https://mangoroom.cn/usr/uploads/2019/07/4097456714.png