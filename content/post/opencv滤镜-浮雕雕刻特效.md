---
title: "opencv滤镜-浮雕雕刻特效"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","浮雕","雕刻" ]
draft: false
slug: "relief-and-carving-filter"
date: "2019-07-03 14:33:00"
---

## 浮雕和雕刻

### 浮雕

浮雕的算法为，用当前像素点的**前一个**像素点灰度值减去**后一个**像素点的灰度值，所得结果加上128作为当前像素点的灰度值。原理的公式为

```
current(i, j) =  current(j+1, j+1) - current(i-1, j-1)
```

用卷积的运算也可以表达为用以下的卷积核与图像做卷积运算

```
 [1  0  0；

  0  0  0；

  0  0  -1 ]
```

### 雕刻

雕刻的算法与浮雕类似，只是在像素点的选择上有所区别。是用**当前**的像素点减去**后一个**像素点的灰度值，所得结果加上128作为当前像素点的灰度值。原理的公式为：

```
current(i, j) =  current(j, j) - current(i-1, j-1)
```

用卷积的运算也可以表达为用以下的卷积核与图像做卷积运算

```
[1  0

 0 -1]
```

---

以上的操作可以形成浮雕或者雕刻的原因在于，由于图片中相邻点的颜色值是比较接近的，因此这样的算法处理之后，只有颜色的边沿区域，也就是相邻颜色差异较大的部分的结果才会比较明显，而其他平滑区域则值都接近128左右，也就是灰色，这样就具有了浮雕或者雕刻效果。

![原图](https://mangoroom.cn/usr/uploads/2019/07/3135764183.jpg)

## 代码实现

```c++
// relief-filter.cpp : 浮雕滤镜
// @mango

#include <iostream>
#include<opencv2/opencv.hpp>

int main()
{

	cv::Mat img = cv::imread("fruit.jpg");

	cv::Mat relief1(img.size(), CV_8UC3);
	cv::Mat relief2(img.size(), CV_8UC3);
	for (size_t i = 1; i < img.rows - 1; i++)
	{
		for (size_t j = 1; j < img.cols - 1; j++)
		{
			for (size_t k = 0; k < 3; k++)
			{
				int res1 = img.at<cv::Vec3b>(i , j )[k] - img.at<cv::Vec3b>(i - 1, j - 1)[k] + 128; // 雕刻
				int res2 = img.at<cv::Vec3b>(i + 1, j + 1)[k] - img.at<cv::Vec3b>(i - 1, j - 1)[k] + 128; //浮雕

				relief1.at<cv::Vec3b>(i, j)[k] = cv::saturate_cast<uchar>(res1);
				relief2.at<cv::Vec3b>(i, j)[k] = cv::saturate_cast<uchar>(res2);
			}
		}
	}
	cv::imshow("雕刻滤镜", relief1);
	cv::imshow("浮雕滤镜", relief2);
	cv::waitKey(0);
	return 0;
}
```

浮雕效果
![浮雕滤镜效果.png][1]
雕刻效果
![雕刻滤镜效果.png][2]

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://mangoroom.cn/opencv/relief-and-carving-filter.html

  [1]: https://mangoroom.cn/usr/uploads/2019/07/2527405033.png
  [2]: https://mangoroom.cn/usr/uploads/2019/07/3292800987.png