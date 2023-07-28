---
title: "opencv滤镜-反向滤镜"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","颜色反转" ]
draft: false
slug: "reversal-filter"
date: "2019-07-02 23:02:00"
---

## 反向滤镜

一般的rgb图像由红色r、绿色g和蓝色b三个颜色分量构成，三个分量的数值一般为8位二进制数，数值范围在[0,255]。所以，将用255减去分量的数值所得的结果替换原来分量的数值称为色彩反向。例如一张图像中某个像素点的r分量数值为2，反向后为255 - 2 = 253，即在[0，255]区间中对调位置、反向了。

![原图](https://mangoroom.cn/usr/uploads/2019/07/3135764183.jpg)

## 代码实现

```c++
	// reversal-filter.cpp 
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
				for (size_t k = 0; k < 3; k++)
				{
					img.at<cv::Vec3b>(i, j)[k] = 255 - img.at<cv::Vec3b>(i, j)[k];
				}
			}
		}

		cv::imshow("反向滤镜", img);
		cv::waitKey(0);
		return 0;
	}

```
运行代码效果

![反向滤镜效果.png][1]

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://mangoroom.cn/opencv/reversal-filter.html

  [1]: https://mangoroom.cn/usr/uploads/2019/07/4197090750.png