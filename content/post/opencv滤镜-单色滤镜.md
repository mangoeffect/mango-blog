---
title: "opencv滤镜-单色滤镜"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","单色" ]
draft: false
slug: "opencv/single-color-filter"
date: "2019-07-03 09:51:00"
---

## 单色滤镜

单色滤镜的原理非常简单，rgb图像有三个颜色分量，单色滤镜只需要将其中两个分量的值设置为0，保留的颜色分量不修改即可。

![filter-img](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//filter-image.jpeg)

## 代码实现

```c++
// single-color-filter.cpp 
// @mango

#include <iostream>
#include<opencv2/opencv.hpp>



int main()
{

	cv::Mat img = cv::imread("fruit.jpg");

	for (size_t i = 0; i < img.rows; i++)
	{
		for (size_t j = 0; j < img.cols; j++)
		{
			// 保留绿色 | remain green
			img.at<cv::Vec3b>(i, j)[0] = 0;// blue
			img.at<cv::Vec3b>(i, j)[2] = 0;// red
		}
	}
	cv::imshow("单色滤镜-绿色", img);

	cv::waitKey(0);
	return 0;
}


```

![单色滤镜-绿色.png][1]

![单色滤镜-红色.png][2]

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/opencv/single-color-filter.html

  [1]: https://mangoroom.cn/usr/uploads/2019/07/3379661975.png
  [2]: https://mangoroom.cn/usr/uploads/2019/07/2612047580.png