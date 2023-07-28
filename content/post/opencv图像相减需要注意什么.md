---
title: "opencv图像相减需要注意什么"
categories: [ "opencv" ]
tags: [ "opencv" ]
draft: false
slug: "notes-for-image-subtraction-in-opencv"
date: "2020-01-19 11:34:00"
---

在opencv中，将两幅图片相减是非常简单的，只需要遍历像素，对应像素点一一相减即可。但是有些点仍需注意，否则就会得到意料之外的结果。

## 灰度级边界检查

opencv中像素灰度级范围一般为【0，255】，如果相减（或者相加）的结果超出这个范围需要做限定。否则就会出现以想不到的结果，如下面的程序：

```c++
// image1 - image2
void subtractImage(cv::Mat& inputImage1, cv::Mat& inputImage2, cv::Mat& outputImage)
{
	for (int i = 0; i < inputImage1.rows; ++i)
	{
		for (int j = 0; j < inputImage1.cols; ++j)
		{
			int val = inputImage1.at<uchar>(i, j) - inputImage2.at<uchar>(i, j);

			outputImage.at<uchar>(i, j) = val;
		}
	}
}
```
程序中是两个输入图像的像素值直接相减，得到结果直接赋予输出图像。试考虑这么一种情况，image1像素值小于image2的像素值，那么相减结果就是负数val，不在
【0，255】范围内，直接赋予输出图像uchar类型的值，结果就会是255 + val。可以在控制台写个测试代码便知道：

```c++
unsigned char a = -1;
std::cout << int(a) << std::endl;
unsigned char a = -2;
std::cout << int(a) << std::endl;
```
结果为：

```
255
254
```
如果是这样的结果就与我们预想的就不一致了，需要进行限制，如果结果值在范围【0，255】左侧，那么以靠近的原则取结果为0。

```c++
// image1 - image2
void subtractImage(cv::Mat& inputImage1, cv::Mat& inputImage2, cv::Mat& outputImage)
{
	for (int i = 0; i < inputImage1.rows; ++i)
	{
		for (int j = 0; j < inputImage1.cols; ++j)
		{
			int val = inputImage1.at<uchar>(i, j) - inputImage2.at<uchar>(i, j);

			if (val < 0)
				outputImage.at<uchar>(i, j) = 0;

			outputImage.at<uchar>(i, j) = val;
		}
	}
}
```

## 相减结果绝对值

如果图像相减的结果不想以最近的原则来处理超范围的结果，想用相减结果额绝对值来作为结果，那么也有需要注意地方。

- 不建议使用 ```cv::abs```函数直接两个mat相减

```c++
cv::Mat absDiff = cv::abs(mat1 - mat2);
```

但这种用法其实容易让人困惑，因为mat1 - mat2的计算调用了```cv::subtract()```，而它对于相减像素结果为负值的处理是val + 255。举个例子：

```
0 - 255 = -255
-255 + 255 = 0
mat1 - mat2 = 0
abs(mat1 - mat2) = 0
```

以上推理利索当然，实际的结果并不是的，mat1 - mat2的计算调用了```cv::subtract()```，但是```cv::abs(mat1 - mat2)```实际调用的却是```cv::absdiff()```。
所以为了避免这种困惑，还不如直接使用```cv::absdiff()```。


## references

- [【1】OpenCV两个Mat相减的隐藏秘密-csdn](https://blog.csdn.net/u012494876/article/details/80629474)
- [【2】OpenCV documents](https://docs.opencv.org/master/)


---

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/opencv/notes-for-image-subtraction-in-opencv.html
