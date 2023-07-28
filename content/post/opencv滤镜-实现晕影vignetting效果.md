---
title: "opencv滤镜-实现晕影vignetting效果"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","晕影" ]
draft: false
slug: "create-vignetting-filter-in-opencv"
date: "2019-07-02 10:32:00"
---

## 晕影vignetting

> 在摄影和光学领域内，**晕影**或暗角是指图像的外围部分的亮度或饱和度比中心区域低。晕影的出现通常是因为相机的设定和镜头的限制因素等，被认为是不希望得到的和非故意的效果，然而，有时却因为需要创意风格而被刻意加入，例如引起对图像中心区的注意。摄影师也会故意选择能产生晕影的镜头来制造特效，也可以使用特别的滤镜或以后期处理来制造晕影。

![Backlight-wedding.jpg][1]

> 晕影的英文“vignetting”源自法语“vignette”，最初是指书本的装饰边，后来则用于描述中间清晰而逐渐消失在边缘的照片。在投影屏上投影出来的照片或者影片也有相类似的效果。

![RioparaguayJune2005.jpg][2]


## 实现原理

实现的思路为：

![beach.jpg][3]

1. 构造一个渐变图像mask

![gradient.png][4]

2. 将渐变的图像与原图融合

![vignette.png][5]

## 代码实现

```c++
// create vignetting filter in opencv | opencv创建晕影滤镜
// @mango

#include<iostream>
#include <math.h>
#include <vector>

// opencv4.1
#include<opencv2/opencv.hpp>


// Helper function to calculate the distance between 2 points. | 帮助器函数计算 2 点之间的距离。
double dist(cv::Point a, cv::Point b)
{
	return sqrt(pow((double)(a.x - b.x), 2) + pow((double)(a.y - b.y), 2));
}

// Helper function that computes the longest distance from the edge to the center point. | 帮助器函数,用于计算从边缘到中心点最远的距离。
double getMaxDisFromCorners(const cv::Size& imgSize, const cv::Point& center)
{
	// given a rect and a line | 给定一个矩形和一条线
	// get which corner of rect is farthest from the line | 得到哪个角的矩形是离线最远


	std::vector<cv::Point> corners(4);
	corners[0] = cv::Point(0, 0);
	corners[1] = cv::Point(imgSize.width, 0);
	corners[2] = cv::Point(0, imgSize.height);
	corners[3] = cv::Point(imgSize.width, imgSize.height);

	double max_dis = 0;
	for (int i = 0; i < 4; ++i)
	{
		double dis = dist(corners[i], center);
		if (max_dis < dis)
			max_dis = dis;
	}

	return max_dis;
}

// Helper function that creates a gradient image.  | 帮助函数用于创建一个渐变的图像
// first_point, radius and power, are variables that control the artistic effect of the filter. | first_point,半径和功率是控制滤波器艺术效果的变量。

void generateGradient(cv::Mat& mask)
{
	cv::Point first_point = cv::Point(mask.size().width / 2, mask.size().height / 2);
	double radius = 1.0;
	double power = 0.6;

	// max image radian | 最大图像半径
	double max_image_rad = radius * getMaxDisFromCorners(mask.size(), first_point);

	mask.setTo(cv::Scalar(1));
	for (int i = 0; i < mask.rows; i++)
	{
		for (int j = 0; j < mask.cols; j++)
		{
			double temp = dist(first_point, cv::Point(j, i)) / max_image_rad;
			temp = temp * power;
			double temp_s = pow(cos(temp), 4);
			mask.at<double>(i, j) = temp_s;
		}
	}
}


int main()
{
	std::cout << "OpenCV version : " << CV_VERSION << std::endl;

	cv::Mat img = cv::imread("beach.jpg");
	if (img.empty()) { return -1; }

	// create a mask | 创建一个mask
	cv::Mat mask_img(img.size(), CV_64F);
	generateGradient(mask_img);

	cv::Mat gradient;
	cv::normalize(mask_img, gradient, 0, 255, 32); //cv::normalize(maskImg, gradient, 0, 255, CV_MINMAX);
	cv::imwrite("gradient.png", gradient);

	cv::Mat lab_img(img.size(), CV_8UC3);
	cv::cvtColor(img, lab_img, cv::COLOR_BGR2Lab);

	for (int row = 0; row < lab_img.size().height; row++)
	{
		for (int col = 0; col < lab_img.size().width; col++)
		{
			cv::Vec3b value = lab_img.at<cv::Vec3b>(row, col);
			value.val[0] *= mask_img.at<double>(row, col);
			lab_img.at<cv::Vec3b>(row, col) = value;
		}
	}

	cv::Mat output;
	cv::cvtColor(lab_img, output, cv::COLOR_Lab2BGR);
	cv::imwrite("vignette.png", output);

	std::string window_name = "Vignette-power0.6";
	cv::namedWindow(window_name, cv::WINDOW_NORMAL);
	cv::resizeWindow(window_name, output.size().width / 2, output.size().height / 2);
	cv::imshow(window_name, output);

	cv::waitKey();

	
	return 0;
}
```

---

本文由芒果浩明发布，转载请注明来源。
本文链接:https://mangoroom.cn/opencv/create-vignetting-filter-in-opencv.html

  [1]: https://mangoroom.cn/usr/uploads/2019/07/1084916241.jpg
  [2]: https://mangoroom.cn/usr/uploads/2019/07/1553471880.jpg
  [3]: https://mangoroom.cn/usr/uploads/2019/07/3816103308.jpg
  [4]: https://mangoroom.cn/usr/uploads/2019/07/329240019.png
  [5]: https://mangoroom.cn/usr/uploads/2019/07/3517065683.png