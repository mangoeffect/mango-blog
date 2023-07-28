---
title: "c++实现matlab灰阶变换imadjust和stretchlim"
categories: [ "opencv","c++","计算机视觉" ]
tags: [ "opencv","c++","matlab","灰度变换" ]
draft: false
slug: "matlab-imadjust-and-stretchlim-function-implement-by-cpp"
date: "2020-03-25 23:18:00"
---

## 灰阶变换
首先介绍一下灰阶变换，一幅图像数据本身在存储的数据结构上为一个二维的矩阵，即一幅图像为m*n个密密麻麻的像素点构成。

![mat-structure.png][1]

然后，这些像素点有着一个值，这个值成为灰度值或者亮度值，值的范围为[0,255]，一共256级的亮度，也有成灰度级或灰阶的说法。那么如果把像素点的坐标（x, y）看作自变量，那么像素点的灰度值就可以看作它的函数值。

![image-coordinate.png][2]

然后**灰阶变换**就是函数值f(x,y)的变换了。就是一副像素点的灰度值按照某种规则或者关系从一个灰度级变换到另一个灰度级，变换的表达式如下。



$$g(x,y) = T[f(x,y)]$$
在matlab中，灰阶变换的两个工具函数，imadjust和stretchlim，接下来我们就用c++实现它。

## imadjust

要实现imadjust函数，就先了解一下它，不知所以然就没法实现。这我们换看百度百科的介绍。

>imadjust是一个计算机函数，该函数用于调节灰度图像的亮度或彩色图像的颜色矩阵。在matlab的命令窗口中键入： doc imadjust或者help imadjust即可获得该函数的帮助信息， 键入type imadjust可以查看函数的源代码。

再看看其函数原型

~~~
J = imadjust(I)

J = imadjust(I,[low_in; high_in],[low_out; high_out])

J = imadjust(I,[low_in; high_in],[low_out; high_out],gamma)

[newmap](https://baike.baidu.com/item/newmap) = imadjust(map,[low_in; high_in],[low_out;high_out],gamma)

RGB2 = imadjust(RGB1,...)

~~~
对应的要实现的c++函数原型是这样的

~~~c++
void imadjust(cv::Mat& input, cv::Mat& output, double low_in = 0.0, double high_in  = 1.0, double low_out = 0.0, double high_out = 1.0, double gamma = 1);//matlab,像素区间[0,1]
void imadjust(cv::Mat& input, cv::Mat& output, std::vector<double> in = { 0.0, 1.0 }, double low_out = 0.0, double high_out = 1.0, double gamma = 1);
void imadjust2(cv::Mat& input, cv::Mat& output, int low_in, int high_in, int low_out, int high_out, double gamma = 1);//opencv，像素区间[0,255]
~~~
接着是函数的功能
**该函数用于调节灰度图像的亮度或彩色图像的颜色矩阵**

来自官方帮助文档的使用示例

~~~matlab
I = imread('pout.tif');J = imadjust(I);imshow(I), figure, imshow(J)
K = imadjust(I,[0.3 0.7],[]);figure, imshow(K)
RGB1 = imread('football.jpg');RGB2 = imadjust(RGB1,[.2 .3 0; .6 .7 1],[]);
imshow(RGB1), figure, imshow(RGB2)
~~~

附上解释

~~~matlab
J = imadjust(I)

将灰度图像 I 中的亮度值映射到 J 中的新值，使得图像中 1% 的数据饱和至最低和最高亮度，这可以增加输出图像 J 的对比度值。此用法相当于 imadjust(I,stretchlim(I))

J = imadjust(I,[low_in; high_in],[low_out; high_out])

将图像I中的亮度值映射到J中的新值，即将low_in至high_in之间的值映射到low_out至high_out之间的值。low_in 以下与 high_in 以上的值被剪切掉了，也就是说，low_in 以下的值映射到 low_out，high_in 以上的值映射到high_out。它们都可以使用空的矩阵[]，默认值是[0 1]。

J = imadjust(I,[low_in; high_in],[low_out; high_out],gamma)

将图像 I 中的亮度值映射到 J 中的新值，其中 gamma指定描述值I和值J关系的曲线形状。如果gamma小于1，此映射偏重更高数值（明亮）输出，如果gamma大于1，此映射偏重更低数值（灰暗）输出，如果省略此参数，默认为(线性映射)。 《Simulink与信号处理》

newmap = imadjust(map,[low_in; high_in],[low_out; high_out],gamma)

调整索引色图像的调色板map。如果low_in, high_in, low_out, high_out 和 gamma 都是标量，那么对 r，g，b 分量同时都做此映射。对于每个颜色分量都有唯一的映射，当 low_in 和 high_in 同时为1*3向量或者 low_out 和 high_out 同时为1*3向量或者 gamma 为1*3向量时。调整后的颜色矩阵 [newmap](https://baike.baidu.com/item/newmap) 和 map 有相同的大小。

RGB2 = imadjust(RGB1,...)

对 RGB 图像 RGB1 的红、绿、蓝调色板分别进行调整。随着颜色矩阵的调整，每一个调色板都有唯一的映射值。

~~~

这里我们着重理解一下行数原型的几个参数
~~~
J = imadjust(I,[low_in; high_in],[low_out; high_out],gamma)
~~~

I为输入的图像，函数的功能就是把输入图像在[low_in; high_in]灰度级中的像素点，映射为[low_out; high_out]区间的灰度级，gamma为一个可选参数，gamma = 1为线性变换，gamma != 1为伽马变换。
![chart-1.png][3]
![chart-2.png][4]
![chart-3.png][5]
下面来讲一下伽马变换。伽马变换又称幂次变换，变换公式为



$$s = cr^{\gamma}$$

![gamma.png][6]

好，下面开始代码的实现。
先用一张表储存伽马变换的值，这样就不需要在每次变量像素点的时候都调用gamma变换公式计算

~~~c++
std::vector<uchar> gammaLut(const double gamma, const double c)
{
	std::vector<uchar> lut(256);
	for (int i = 0; i < 256; ++i)
		lut[i] =  static_cast<uchar>(c * std::pow((double)(i / 255.0), gamma) * 255.0);

	return lut;
}
~~~

然后遍历图像，对每个像素点进行灰阶变换操作

~~~c++
for (int i = 0; i < rows; ++i)
			for (int j = 0; j < cols; ++j)
			{
				double result = 0;
				if (input.at<uchar>(i, j) <= low_in)//灰度值小于low_in的像素点
				{
					result = low_out;//结果为low_out
				}
				else if (low_in < input.at<uchar>(i, j) && input.at<uchar>(i, j) < high_in)//灰度值在[low_in, high_in]
				{
					result = k * (input.at<uchar>(i, j) - low_in) + high_in;//灰度值线性变换
					result = gamma_lut[static_cast<uchar>(result)];//灰度值gamma变换
				}	
				else
				{
					result = high_out;//灰度值大于high_in的像素点，结果为high_out
				}

				output.at<uchar>(i, j) = static_cast<uchar>(result) % 255;
			}
~~~


### 代码
下面放出完整代码

~~~c++
//imadjust函数的实现

#include<opencv2/opencv.hpp>
#include<iostream>
#include<cassert>
#include<vector>

void imadjust(cv::Mat& input, cv::Mat& output, double low_in = 0.0, double high_in  = 1.0, double low_out = 0.0, double high_out = 1.0, double gamma = 1);//matlab,像素区间[0,1]
void imadjust(cv::Mat& input, cv::Mat& output, std::vector<double> in = { 0.0, 1.0 }, double low_out = 0.0, double high_out = 1.0, double gamma = 1);
void imadjust2(cv::Mat& input, cv::Mat& output, int low_in, int high_in, int low_out, int high_out, double gamma = 1);//opencv，像素区间[0,255]
std::vector<uchar> gammaLut(const double gamma, const double c = 1.0);//灰度值的伽马变换结果表lut

bool is0to1(const double var);

int main()
{
	cv::Mat src_img = cv::imread("Fig0304(a)(breast_digital_Xray).tif");
	if(src_img.empty()) return -1;

	cv::Mat dst_img;
	imadjust(src_img, dst_img, 0, 1, 0, 1, 2);

	cv::imshow("src_img", src_img);

	cv::imshow("dst_img", dst_img);
	cv::waitKey(0);

	return 0;
}//main

void imadjust(cv::Mat& input, cv::Mat& output, double low_in, double high_in, double low_out, double high_out, double gamma)
{
	assert(low_in < high_in && is0to1(low_in) && is0to1(high_in) && is0to1(low_out) && is0to1(high_out));

	//将matlab中的灰度值区间[0,1]转为opencv灰度值区间[0,255]
	high_in *= 255; high_out *= 255; low_in *= 255; low_out *= 255;

	imadjust2(input, output, low_in, high_in, low_out, high_out, gamma);
}
void imadjust(cv::Mat& input, cv::Mat& output, std::vector<double> in, double low_out, double high_out, double gamma)
{
	assert(2 == in.size());
	double low_in = in[0];
	double high_in = in[1];
	imadjust(input, output, low_in, high_in, low_out, high_out, gamma);
}


void imadjust2(cv::Mat& input, cv::Mat& output, int low_in, int high_in, int low_out, int high_out, double gamma )//opencv，像素区间[0,255]
{
	output = input.clone();
	int rows = input.rows;//行
	int cols = input.cols;//列
	double k = (static_cast<double>(high_out) - low_out) / (high_in - low_in);
	std::vector<uchar> gamma_lut = gammaLut(gamma);

	switch(input.channels())
	{

	case 1://灰度图
		
		for (int i = 0; i < rows; ++i)
			for (int j = 0; j < cols; ++j)
			{
				double result = 0;
				if (input.at<uchar>(i, j) <= low_in)//灰度值小于low_in的像素点
				{
					result = low_out;//结果为low_out
				}
				else if (low_in < input.at<uchar>(i, j) && input.at<uchar>(i, j) < high_in)//灰度值在[low_in, high_in]
				{
					result = k * (input.at<uchar>(i, j) - low_in) + high_in;//灰度值线性变换
					result = gamma_lut[static_cast<uchar>(result)];//灰度值gamma变换
				}	
				else
				{
					result = high_out;//灰度值大于high_in的像素点，结果为high_out
				}

				output.at<uchar>(i, j) = static_cast<uchar>(result) % 255;
			}
		break;

		//彩色图片
	case 3:
		for (int i = 0; i < rows; ++i)
			for (int j = 0; j < cols; ++j)
				for (int k = 0; k < 3; ++k)
				{
					double result = 0;
					if (input.at<cv::Vec3b>(i, j)[k] <= low_in)
						result = low_out;
					else if (low_in < input.at<cv::Vec3b>(i, j)[k] && input.at<cv::Vec3b>(i, j)[k] < high_in)
					{
						result = k * (input.at<cv::Vec3b>(i, j)[k] - low_in) + high_in;
						result = gamma_lut[static_cast<uchar>(result)];
					}
					else
					{
						result = high_out;
					}

					output.at<cv::Vec3b>(i, j)[k] = static_cast<uchar>(result) % 255;
				}		
		break;

	default:
		break;
	}
}


bool is0to1(const double var)
{
	return 0 <= var && var <= 1;
}

std::vector<uchar> gammaLut(const double gamma, const double c)
{
	std::vector<uchar> lut(256);
	for (int i = 0; i < 256; ++i)
		lut[i] =  static_cast<uchar>(c * std::pow((double)(i / 255.0), gamma) * 255.0);

	return lut;
}
~~~
测试效果

![result.png][7]



## stretchlim

同样的，先看看这个函数有什么作用，stretchlim函数要用于自适应找到一个分割阈值向量来改变一幅图像的对比度。就是自动找到imadjust函数中[low_in; high_in]的合适值，所以说stretchlim是为imadjust函数服务的。

接着来看一下stretchlim函数的原型

~~~
lowhigh = stretchlim(I)
lowhigh = stretchlim(I,Tol)
~~~
对应到我们要实现的c++函数原型是这样的

~~~c++
std::vector<double> strecthlim(cv::Mat img, double tol_low = 0.01, double tol_high = 0.99);
~~~

至于函数的实现原理和过程，看到这篇文章[Matlab中的stretchlim函数详解](http://blog.sina.com.cn/s/blog_14d1511ee0102wwb6.html)，解释的非常清楚。我就不详细说明了,代码中也给了较多的注释。

### 代码
下面贴上代码

头文件
~~~c++
#pragma once
#include<opencv2/opencv.hpp>
#include<vector>
#include<cassert>

std::vector<int> calcGrayLevel(cv::Mat& img);//计算灰度级，即算出从0到255区间的任意一个灰度值i，在图像img中有多少个像素点的灰度值为i
std::vector<double> pdf(std::vector<int> gray_level, cv::Mat& img);//计算概率密度pdf
std::vector<double> cdf(std::vector<double> pdf);//计算概率分布cdf
double findLow(double input_low, std::vector<double> cdf);
double findHihg(double input_high, std::vector<double> cdf);
std::vector<double> strecthlim(cv::Mat img, double tol_low = 0.01, double tol_high = 0.99);
~~~

cpp文件

~~~c++
//2-2-1
//stretchlim函数

#include"stretchlim.h"
#include<iostream>


int main()
{
	cv::Mat img = cv::imread("./Fig0304(a)(breast_digital_Xray).tif", 0);

	if (img.empty()) return -1;

	std::vector<double> v = strecthlim(img);
	for (auto& i : v)
		std::cout << i << std::endl;

	system("pause");
	return 0;
}

std::vector<int> calcGrayLevel(cv::Mat& img)//计算灰度级，即算出从0到255区间的任意一个灰度值i，在图像img中有多少个像素点的灰度值为i
{
	assert(img.channels() == 1);//只计算灰度图像的
	std::vector<int> res(256);
	int rows = img.rows;//行
	int cols = img.cols;//列

	for(int i = 0; i < rows; ++i)
		for (int j = 0; j < cols; ++j)
		{
			int val = img.at<uchar>(i, j);
			res[val] += 1;
		}

	return res;
}


std::vector<double> pdf(std::vector<int> gray_level, cv::Mat& img)//计算概率密度
{
	assert(gray_level.size() == 256);
	
	int N = img.rows * img.cols;//像素点总数
	std::vector<double> res(256);
	for (int i = 0; i < 256; ++i)
		res[i] =static_cast<double>(gray_level[i]) / N;

	return res;
}

std::vector<double> cdf(std::vector<double> pdf)//计算概率分布cdf
{
	assert(pdf.size() == 256);

	std::vector<double> res(256);
	res[0] = pdf[0];
	for (int i = 1; i < 256; ++i)
		res[i] = pdf[i] + res[i - 1];
	return res;
}


double findLow(double input_low, std::vector<double> cdf)
{
	assert(cdf.size() == 256);
	//找到分布概率大于我们的输入值input_low处最接近的灰度值，并以此作为最佳分割阈值的最小值
	for (int i = 0; i < 256; ++i)
		if (cdf[i] > input_low)
			return cdf[i];

	return 0.0;
}
double findHihg(double input_high, std::vector<double> cdf)
{
	assert(256 == cdf.size());
	//找到分布概率大于或等于我们的输入值input_high处最接近的灰度值，并以此作为最佳分割阈值的最大值
	for(int i = 0; i < 256; ++i)
		if(cdf[i] >= input_high)
			return cdf[i];

	return 0.0;
}


std::vector<double> strecthlim(cv::Mat img, double tol_low, double tol_high)
{
	std::vector<double> v(2);
	if (img.empty()) return v;

	//计算灰度值
	std::vector<int> gray_level = calcGrayLevel(img);
	//计算概率密度pdf
	std::vector<double> p = pdf(gray_level, img);
	//计算概率分布cdf
	std::vector<double> c = cdf(p);
	//寻找tol_low, tol_high
	tol_low = findLow(tol_low, c);
	tol_high = findHihg(tol_high, c);
	
	if (tol_low == tol_high)
		v = { 0.0, 1.0 };
	else
		v = { tol_low, tol_high };

	return v;
}
~~~

## reference

【1】数字图像处理第三版
【2】OpenCV官方文档


-----

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/opencv/matlab-imadjust-and-stretchlim-function-implement-by-cpp.html

  [1]: https://mangoroom.cn/usr/uploads/2020/03/896254952.png
  [2]: https://mangoroom.cn/usr/uploads/2020/03/3321482999.png
  [3]: https://mangoroom.cn/usr/uploads/2020/03/3658682747.png
  [4]: https://mangoroom.cn/usr/uploads/2020/03/1300775181.png
  [5]: https://mangoroom.cn/usr/uploads/2020/03/2854185200.png
  [6]: https://mangoroom.cn/usr/uploads/2020/03/2071168353.png
  [7]: https://mangoroom.cn/usr/uploads/2020/03/4235966228.png