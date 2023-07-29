---
title: "opencv滤镜-图像灰度化"
categories: [ "opencv","滤镜" ]
tags: [ "opencv","图像处理","图像处理算法","滤镜","灰度" ]
draft: false
slug: "opencv/image-graying-in-opencv"
date: "2019-07-02 16:29:00"
---

## 灰度图像

灰度化，在RGB模型中，如果R=G=B时，则彩色表示一种灰度颜色，其中R=G=B的值叫灰度值，因此，灰度图像每个像素只需一个字节存放灰度值（又称强度值、亮度值），灰度范围为0-255。

图像的灰度化一般作为图像的预处理步骤，为之后更复杂的图像处理做准备。另一方面，将图像灰度化也可以作为一个简常见的滤镜效果。

## 灰度化方法

一般将图像灰度化由分量法、最大值法、平均值发以及加权平均法4种。
![bowl-of-fruit-in-rain-4125348_640.jpg][1]

### 1-分量法

分量法是指将图像的三种分量的亮度值（灰度值）作为三个图像的灰度值的方法，可以根据需要选择应用哪一个分量产生的灰度图像。该方法的公式原理如下：

![分量法.png][2]

#### 代码实现

```c++
// channel graying | 分量法
enum enumRGB { R = 1, G = 2, B = 3 };
cv::Mat channelGraying(cv::Mat src_image, enumRGB channel)
{
	cv::Mat gray_image(src_image.size(), CV_8UC1);

	switch (channel)
	{
	case R:
		for (size_t i = 0; i < src_image.rows; i++)
		{
			for (size_t j = 0; j < src_image.cols; j++)
			{
				gray_image.at<uchar>(i, j) = src_image.at<cv::Vec3b>(i, j)[2];
			}
		}

		break;
	case G:
		for (size_t i = 0; i < src_image.rows; i++)
		{
			for (size_t j = 0; j < src_image.cols; j++)
			{
				gray_image.at<uchar>(i, j) = src_image.at<cv::Vec3b>(i, j)[1];
			}
		}

	case B:
		for (size_t i = 0; i < src_image.rows; i++)
		{
			for (size_t j = 0; j < src_image.cols; j++)
			{
				gray_image.at<uchar>(i, j) = src_image.at<cv::Vec3b>(i, j)[0];
			}
		}
		break;
	default:
		assert(false);
		break;
	}

	return gray_image;
}
```
运行代码效果
![分量法灰度化效果.png][3]
### 2-最大值法

最大值法为，选取三个分量中亮度值（灰度值）最大的作为灰度图像的灰度值。该方法公式原理如下：

![最大值法.png][4]

#### 代码实现

```c++

// max value graying | 最大值灰度化
cv::Mat maxValueGraying(cv::Mat src_image)
{
	cv::Mat gray_image(src_image.size(), CV_8UC1);

	for (size_t i = 0; i < src_image.rows; i++)
	{
		for (size_t j = 0; j < src_image.cols; j++)
		{
			gray_image.at<uchar>(i, j) = std::max(
				src_image.at<cv::Vec3b>(i, j)[0],
				std::max(
					src_image.at<cv::Vec3b>(i, j)[1],
					src_image.at<cv::Vec3b>(i, j)[2]
				)
			);

		}
	}

	return gray_image;
}
```
运行代码效果
![最大值法灰度化效果.png][5]
### 3-平均值法

平均值法为，将三个分量的灰度值求取平均值作为灰度图像的灰度值。该方法的公式原理为：

![平均值法.png][6]
#### 代码实现

```c++
// average value garying | 平均值法
cv::Mat avergaeValueGraying(cv::Mat src_image)
{
	cv::Mat gray_image(src_image.size(), CV_8UC1);

	for (size_t i = 0; i < src_image.rows; i++)
	{
		for (size_t j = 0; j < src_image.cols; j++)
		{
			gray_image.at<uchar>(i, j) = (src_image.at<cv::Vec3b>(i, j)[0] + src_image.at<cv::Vec3b>(i, j)[1] + src_image.at<cv::Vec3b>(i, j)[1]) / 3;
				
		}
	}

	return gray_image;
}
```
![平均值法灰度化效果.png][7]
### 4-加权平均法

根据重要性及其它指标，将三个分量以不同的权值进行加权平均。由于人眼对绿色的敏感最高，对蓝色敏感最低，因此，按下式对RGB三分量进行加权平均能得到较合理的灰度图像。

![加权平均法.png][8]
#### 代码实现

```c++
// weighted average graying| 加权平均法
cv::Mat weightedAvergaeGraying(cv::Mat src_image)
{
	cv::Mat gray_image(src_image.size(), CV_8UC1);

	for (size_t i = 0; i < src_image.rows; i++)
	{
		for (size_t j = 0; j < src_image.cols; j++)
		{
			gray_image.at<uchar>(i, j) = 0.11 * src_image.at<cv::Vec3b>(i, j)[0] + 0.59*src_image.at<cv::Vec3b>(i, j)[1] + 0.30 * src_image.at<cv::Vec3b>(i, j)[2];

		}
	}

	return gray_image;
}
```

![加权平均法灰度化效果.png][9]
## opencv灰度化图像方法

opencv实现图像的灰度化非常简单，有两种方法。

方法1，再读取图片的时候以灰度图像的方式读取图片

```c++
cv::Mat gray_image = cv::imread("test.jpg", 0)
```
方法2， 利用cvColor函数转换

```c++
cv::cvtColor(rgbMat, grayMat, CV_BGR2GRAY); //opencv2.x
cv::cvtColor(rgbMat, grayMat, COLOR_BGR2GRAY); //opencv3.x
```

--- 

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/opencv/image-graying-in-opencv.html

  [1]: https://mangoroom.cn/usr/uploads/2019/07/3135764183.jpg
  [2]: https://mangoroom.cn/usr/uploads/2019/07/3368344024.png
  [3]: https://mangoroom.cn/usr/uploads/2019/07/4242661371.png
  [4]: https://mangoroom.cn/usr/uploads/2019/07/3533021479.png
  [5]: https://mangoroom.cn/usr/uploads/2019/07/685991270.png
  [6]: https://mangoroom.cn/usr/uploads/2019/07/3545741963.png
  [7]: https://mangoroom.cn/usr/uploads/2019/07/2141635783.png
  [8]: https://mangoroom.cn/usr/uploads/2019/07/3634879993.png
  [9]: https://mangoroom.cn/usr/uploads/2019/07/2138888478.png