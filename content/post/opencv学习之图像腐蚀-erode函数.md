---
title: "opencv学习之图像腐蚀-erode函数"
categories: [ "opencv" ]
tags: [ "opencv","图像处理","计算机视觉","erode" ]
draft: false
slug: "opencv-learning-erode"
date: "2019-06-13 15:50:00"
---


**opencv图像腐蚀erode**

## 什么是图像腐蚀

### 形态学运算

形态学（morphology）一词通常表示生物学的一个分支，该分支主要研究动植物的形态和结构。而我们图像处理中指的形态学，往往表示的是数学形态学  
数学形态学（Mathematical morphology）
是一门建立在格论和拓扑学基础之上的图像分析学科，是数学形态学图像处理的基本理论。其基本的运算包括：腐蚀和膨胀、开运算和闭运算、骨架抽取、极限腐蚀、击中击不中变换、形态学梯度、Top-
hat变换、颗粒分析、流域变换等。  
所以，简单的说，形态学运算是对图像基于形状的一系列操作，而图像腐蚀则是其中的一种。

### 腐蚀运算原理

定义结构元素B为：


​    
    1	1
    
    1	0  

图像元素与结构元素相乘，从而求得右下角元素值  
（i-1,j+1） （i,j+1）  
（i-1,j） 所求此点（i,j）

⑴ 用结构元素B，扫描图像A的每一个像素  
⑵ 用结构元素与其覆盖的二值图像做“与”操作  
⑶ 如果都为0，结果图像的该像素为0。否则为255  
腐蚀处理就是求局部最小值的操作，的结果是使原来的二值图像减小一圈。宏观上的效果就是二值图中黑色区域寝室掉白色区域。

原理实现：


​    
```c++
static void g_erosion(int, void*)  

{  

    erosion = Mat::zeros(binarygray.rows, binarygray.cols, binarygray.type());  

    {  

        for (int i = 1; i < binarygray.rows; i++)  

        {  

            for (int j = 1; j < binarygray.cols; j++)  

            {  

                if (binarygray.data[(i - 1)*binarygray.step + j] + binarygray.data[(i - 1)*binarygray.step + j + 1] + binarygray.data[i*binarygray.step + j + 1] == 0)  

                {  

                    erosion.data[i*erosion.step + j] = 0;  

                }  

                else  

                {  

                    erosion.data[i*erosion.step + j] = 255;  

                }  

            }  
```

            } 

```c++
    }  

    imshow("erosion_1", erosion);  

}  
```

### 腐蚀运算用途


​    

图像的形态学操作可以实现很多功能，而腐蚀往往结合膨胀一起使用。实现以下功能：  

  1. 消除噪声
  2. 分割(isolate)出独立的图像元素，在图像中连接(join)相邻的元素。
  3. 寻找图像中的明显的极大值区域或极小值区域
  4. 求出图像的梯度

## erode函数

### 函数原型

opencv中腐蚀函数erode的原型如下：


​    
```c++
void cv::erode	(	InputArray 	src,

OutputArray 	dst,

InputArray 	kernel,

Point 	anchor = Point(-1,-1),

int 	iterations = 1,

int 	borderType = BORDER_CONSTANT,

const Scalar & 	borderValue = morphologyDefaultBorderValue() 

)  
```

### 参数解释

  1. 第一个参数，InputArray类型的src，输入图像，即源图像，填Mat类的对象即可。图像通道的数量可以是任意的，但图像深度应为CV_8U，CV_16U，CV_16S，CV_32F或 CV_64F其中之一。
  2. 第二个参数，OutputArray类型的dst，即目标图像，需要和源图片有一样的尺寸和类型。
  3. 第三个参数，InputArray类型的kernel，膨胀操作的核。若为NULL时，表示的是使用参考点位于中心3x3的核。
  4. 第四个参数，Point类型的anchor，锚的位置，其有默认值（-1，-1），表示锚位于中心。
  5. 第五个参数，int类型的iterations，迭代使用erode（）函数的次数，默认值为1。
  6. 第六个参数，int类型的borderType，用于推断图像外部像素的某种边界模式。注意它有默认值BORDER_DEFAULT。
  7. 第七个参数，const Scalar&类型的borderValue，当边界为常数时的边界值，有默认值morphologyDefaultBorderValue()，一般我们不用去管他。需要用到它时，可以看官方文档中的createMorphologyFilter()函数得到更详细的解释。

使用erode函数，一般我们只需要填前面的三个参数，后面的四个参数都有默认值。。

### 使用示例


​    
```c++
// 2_image_erode.cpp: 定义控制台应用程序的入口点。

//date: 2018/03/21

//editor: ihaoming

//include files

#include "stdafx.h"

#include<iostream>

#include<opencv2/highgui/highgui.hpp>

#include<opencv2/imgproc/imgproc.hpp>

//namespace

using namespace std;

using namespace cv;

int main()

{

	Mat src_image = imread("./clock.jpg");

	if (src_image.data == NULL)

	{

		cout << "can&apos;t open the file!" << endl;

		getchar();

		return -1;

	}

	else

	{

		imshow("src_image", src_image);

		//get structuring element

		Mat element_1 = getStructuringElement(MORPH_RECT, Size(15, 15));

		Mat element_2 = getStructuringElement(MORPH_CROSS, Size(15, 15));

		Mat element_3 = getStructuringElement(MORPH_ELLIPSE, Size(15, 15));

		Mat dst_image_1;

		Mat dst_image_2;

		Mat dst_image_3;

		//erode the image

		erode(src_image, dst_image_1, element_1);

		erode(src_image, dst_image_2, element_2);

		erode(src_image, dst_image_3, element_3);

		imshow("dst_image", dst_image_1);

		imshow("dst_image_2", dst_image_2);

		imshow("dst_image_3", dst_image_3);

		waitKey(0);

	}

	return 0;

}  
```
