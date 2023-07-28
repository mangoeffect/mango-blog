---
title: "opencv滤镜-怀旧滤镜"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","怀旧" ]
draft: false
slug: "vintage-filter"
date: "2019-07-03 10:22:00"
---

## 怀旧滤镜

通过对rgb三个颜色分量的调整可以将照片处理成一种老照片的怀旧风格。调整的公式如下

![怀旧滤镜公式.png][1]


![原图](https://mangoroom.cn/usr/uploads/2019/07/3135764183.jpg)

## 代码实现

```c++
// vintage-filter.cpp : 怀旧滤镜
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
			
			img.at<cv::Vec3b>(i, j)[1] = cv::saturate_cast<uchar>(0.349*img.at<cv::Vec3b>(i, j)[2] + 0.686*img.at<cv::Vec3b>(i, j)[1] + 0.168*img.at<cv::Vec3b>(i, j)[0]);// green
			img.at<cv::Vec3b>(i, j)[2] = cv::saturate_cast<uchar>(0.393*img.at<cv::Vec3b>(i, j)[2] + 0.769*img.at<cv::Vec3b>(i, j)[1] + 0.189*img.at<cv::Vec3b>(i, j)[0]);// red
                        img.at<cv::Vec3b>(i, j)[0] = cv::saturate_cast<uchar>(0.272*img.at<cv::Vec3b>(i, j)[2] + 0.534*img.at<cv::Vec3b>(i, j)[1] + 0.131*img.at<cv::Vec3b>(i, j)[0]);// blue
		}
	}
	cv::imshow("怀旧滤镜", img);

	cv::waitKey(0);
	return 0;
}

```

运行代码效果

![怀旧滤镜效果.png][2]

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://mangoroom.cn/opencv/vintage-filter.html

  [1]: https://mangoroom.cn/usr/uploads/2019/07/2087852825.png
  [2]: https://mangoroom.cn/usr/uploads/2019/07/1926101731.png