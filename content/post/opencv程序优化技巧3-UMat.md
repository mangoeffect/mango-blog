---
title: "opencv程序优化技巧3-UMat"
categories: [ "opencv","c++","计算机视觉" ]
tags: [ "opencv","opencl","umat","gpu" ]
draft: false
slug: "optimization-methods-in-opencv-3"
date: "2022-05-16 21:53:31"
---

## 简介

前面两篇文章介绍OpenCV常用的优化编码技巧与编译优化选项，除了CUDA以外都是基于CPU的优化技巧。其实OpenCV还支持了OpenCL作为后端的GPU优化,在拥有独立显卡的设备上使用OpenCV，可以考虑开启OpenCL优化。

## UMat使用

在OpenCV中开启OpenCL优化方法非常简单，只需要将Mat类替换未UMat类即可，OpenCV的算法API对于Mat与UMat都是兼容的。下面是UMat的使用示例：

```cpp
//构造UMat
cv::Mat img = cv::UMat(1000, 1000, CV_8UC1); //Mat
cv::UMat uimg = cv::UMat(1000, 1000, CV_8UC1);//UMat

//读取图像
cv::Mat img = cv::imread("./demo.jpg", 0);
cv::UMat uimg = cv::imread("./demo.jpg", 0).getUMat(cv::ACCESS_READ);

//调用算子
cv::medianBlur(img, dst, 5);
cv::medianBlur(uimg, udst, 5);

//UMat与Mat互转
cv::UMat uimg = img.getUMat(cv::ACCESS_READ);
cv::Mat img = uimg.getMat(cv::ACCESS_RW);
```

## 性能测试

以常见的滤波算子，对比原生采用CPU的Mat类进行性能测试对比，对比方法为：构造常见分辨率规格的图像，对图像分别进行滤波处理，每个规格处理测量10次取测量耗时。图像规格取如下的规格

```cpp
{640, 480}, {1280, 720}, {1280, 960}, {1920, 1080}, {1600, 1200}, {2048, 1536}, {2592, 1944}, {3264, 2448}, {3840, 2160}, {4224, 3168}, {5344, 4106}
```

在

- Intel(R) Core(TM) i5-7400 CPU @3.00GHZ
- NVIDIA GeForce GT 730
- OpenCV 4.5.4(tbb)

的测试平台表现如下

### 阈值化

![threshold](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-blog-imagesmango-blog-imagesthreshold.png)

### 均值滤波

![blur](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-blog-imagesmango-blog-imagesblur.png)

### 中值滤波

![medianblur](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-blog-imagesmango-blog-imagesmedianblur.png)

### 高斯滤波



![gaussianblur](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-blog-imagesmango-blog-imagesGaussianBlur.png)


可以看到，对于绝大部分情况。开启了OpenCL的优化后效率还是非常明显的。但也存在可能纯CPU反而比使用GPU的OpenCL的效率要慢的情况。所以无脑直接选择开启OpenCL优化不一定取得很好的效果，一般建议对比测试后选择是否开启OpenCL优化。一般开启OpenCL的时候需要注意以下几点：

- UMat使用GPU的时候是需要从GPU分配内存的，从GPU分配内存速度是比较慢的。所以耗时很小的操作就没必要开启GPU优化，无论是OpenCL还是CUDA模块。再者既然GPU分配内存速度比较缓慢，在使用UMat的时候就需要注意避免重复拷贝的操作。
- 使用UMat并不一定就会采用GPU运算，图像规格需要注意保持在偶数的情况，若是奇数的分辨率规格很可能会退化到采用CPU计算。

## 测试程序

```cpp
/**
 * @file main.cpp
 * @author mango (2321544362@qq.com)
 * @brief 
 * @version 0.1
 * @date 2022-05-14
 * 
 * @copyright Copyright (c) 2022
 * 
 */

#include "opencv2/opencv.hpp"
#include "matplot/matplot.h"

#include <iostream>
#include <chrono>
#include <tuple>
#include <vector>

int main(int argc, char** argv)
{
    std::vector<std::tuple<size_t, size_t>> img_sizes = { {640, 480}, {1280, 720}, {1280, 960}, {1920, 1080}, {1600, 1200}, {2048, 1536}, {2592, 1944}, {3264, 2448}, {3840, 2160}, {4224, 3168}, {5344, 4106} };
    std::vector<double> mat_cost_time;
    std::vector<double> umat_cost_time;
    std::vector<int> plotx;
    for (auto&& [len, wid] : img_sizes)
    {
        cv::Mat img = cv::Mat(len, wid, CV_8UC1);
        cv::UMat uimg = cv::UMat(len, wid, CV_8UC1);
        cv::Mat dst;
        cv::UMat udst;
        auto t0 = std::chrono::system_clock::now();
        for (int i = 0; i < 10; i++)
        {
            cv::GaussianBlur(img, dst, cv::Size(7, 7), 1.5);
            //cv::blur(img, dst, cv::Size(7, 7));
            //cv::threshold(img, dst, 128, 255, cv::THRESH_BINARY);
            //cv::medianBlur(img, dst, 5);
        }
        auto t1 = std::chrono::system_clock::now();
        for (int i = 0; i < 10; i++)
        {
            cv::GaussianBlur(uimg, udst, cv::Size(7, 7), 1.5);
            //cv::blur(uimg, udst, cv::Size(7, 7));
            //cv::threshold(uimg, udst, 128, 255, cv::THRESH_BINARY);
            //cv::medianBlur(uimg, udst, 5);
        }
        auto t2 = std::chrono::system_clock::now();
        double dt1 = std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0).count() / 10.0;
        double dt2 = std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() / 10.0;
        mat_cost_time.push_back(dt1);
        umat_cost_time.push_back(dt2);
        plotx.push_back(len * wid);
    }

    matplot::title("umat GaussianBlur performance");
    matplot::hold(matplot::on);
    auto mat_y = matplot::plot(mat_cost_time, "r");
    auto umat_y = matplot::plot(umat_cost_time, "g");
    matplot::legend({ mat_y,umat_y }, { "mat", "umat"});
    matplot::ylabel("time / ms");
    matplot::show();
    return 0;
}
```
----------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/opencv/optimization-methods-in-opencv-3.html
