---
title: "OpenCV像素访问性能测试"
categories: [ "opencv" ]
tags: [ "opencv","opencv调试" ]
draft: false
slug: "test-access-pixels-efficiency-on-opencv"
date: "2020-05-21 21:02:00"
---

测试OpenCV几种常用的像素访问性能：

```c++
#include <iostream>

#include<opencv2/opencv.hpp>

void AccessByPtr(cv::Mat img)
{
	size_t rows = img.rows;
	size_t cols = img.cols * img.channels();

	for (size_t i = 0; i < rows; i++)
	{
		uchar* pixel = img.ptr<uchar>(i);
		for (size_t j = 0; j < cols; j++)
		{
			pixel[j] = std::log(pixel[j] + 1);
		}
	}
}

void AccessByItera(cv::Mat img)
{
	auto pixel = img.begin<cv::Vec3b>();
	auto pixelEnd = img.end<cv::Vec3b>();

	for(; pixel != pixelEnd; pixel++)
	{
		(*pixel)[0] = std::log((*pixel)[0] + 1);
		(*pixel)[1] = std::log((*pixel)[1] + 1);
		(*pixel)[2] = std::log((*pixel)[2] + 1);
	}
}

void AccessByIndex(cv::Mat img)
{
	size_t rows = img.rows;
	size_t cols = img.cols;

	for (size_t i = 0; i < rows; i++)
	{
		for (size_t j = 0; j < cols; j++)
		{
			img.at<cv::Vec3b>(i, j)[0] = std::log(img.at<cv::Vec3b>(i, j)[0] + 1);
			img.at<cv::Vec3b>(i, j)[1] = std::log(img.at<cv::Vec3b>(i, j)[1] + 1);
			img.at<cv::Vec3b>(i, j)[2] = std::log(img.at<cv::Vec3b>(i, j)[2] + 1);
		}
	}
}

void AccessByLut(cv::Mat img, cv::Mat lut, cv::Mat out)
{
	cv::LUT(img, lut, out);
}

int main()
{
	cv::Mat img = cv::imread("rocket.jpg");

	uchar lutData[256];
	for (size_t i = 0; i < 256; i++)
	{
		lutData[i] = std::log(i + 1);
	}

	cv::Mat lut(1, 256, CV_8UC1, lutData);
    cv::Mat out;

	int testTimes = 1;
	if (!img.empty() && img.channels()!=1)
	{
		std::cout << "OpenCV access pixels test." << std::endl;
#ifdef _DEBUG
		std::cout << "Debug-x64: test " << testTimes << " times" << std::endl;
#else
		std::cout << "Release-x64: test " << testTimes << " times" << std::endl;
#endif // DEBUG

		std::cout << "Image infomation: " << std::endl;
		std::cout << "with: " << img.cols << " height: " << img.rows << " channels: " << img.channels() << std::endl;

		clock_t ts, te;

		ts = clock();
		for (size_t i = 0; i < testTimes; i++)
		{
			AccessByIndex(img);
		}
		te = clock();
		std::cout << "AccessByIndex: " << static_cast<double>(te - ts) << " ms" << std::endl;

		ts = clock();
		for (size_t i = 0; i < testTimes; i++)
		{
			AccessByItera(img);
		}
		te = clock();
		std::cout << "AccessByItera: " << static_cast<double>(te - ts) << " ms" << std::endl;

		ts = clock();
		for (size_t i = 0; i < testTimes; i++)
		{
			AccessByPtr(img);
		}
		te = clock();
		std::cout << "AccessByPtr: " << static_cast<double>(te - ts) << " ms" << std::endl;

		ts = clock();
		//AccessByLut(img, lut, out);
		for (size_t i = 0; i <testTimes; i++)
		{
			cv::LUT(img, lut, out);
		}
		te = clock();
		std::cout << "AccessByLut: " << static_cast<double>(te - ts) << " ms" << std::endl;


	}
}



```


```
OpenCV access pixels test.
Debug-x64: test 10 times
Image infomation:
with: 1920 height: 1200 channels: 3
AccessByIndex: 13675 ms
AccessByItera: 11501 ms
AccessByPtr: 2305 ms
AccessByLut: 48 ms
```

```
OpenCV access pixels test.
Release-x64: test 10 times
Image infomation:
with: 1920 height: 1200 channels: 3
AccessByIndex: 1409 ms
AccessByItera: 1480 ms
AccessByPtr: 1486 ms
AccessByLut: 24 ms
```

```
OpenCV access pixels test.
Debug-x64: test 5 times
Image infomation:
with: 1920 height: 1200 channels: 3
AccessByIndex: 6246 ms
AccessByItera: 5690 ms
AccessByPtr: 1161 ms
AccessByLut: 30 ms
```

```
OpenCV access pixels test.
Release-x64: test 5 times
Image infomation:
with: 1920 height: 1200 channels: 3
AccessByIndex: 666 ms
AccessByItera: 745 ms
AccessByPtr: 741 ms
AccessByLut: 17 ms
```

```
OpenCV access pixels test.
Debug-x64: test 1 times
Image infomation:
with: 1920 height: 1200 channels: 3
AccessByIndex: 1308 ms
AccessByItera: 1097 ms
AccessByPtr: 204 ms
AccessByLut: 12 ms
```

```
OpenCV access pixels test.
Release-x64: test 1 times
Image infomation:
with: 1920 height: 1200 channels: 3
AccessByIndex: 125 ms
AccessByItera: 124 ms
AccessByPtr: 122 ms
AccessByLut: 13 ms
```
