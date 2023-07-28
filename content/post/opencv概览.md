---
title: "opencv概览"
categories: [ "opencv" ]
tags: [ "opencv","图像处理","计算机视觉" ]
draft: false
slug: "opencv-overview"
date: "2019-06-15 08:11:00"
---

## 简介
OpenCV的全称为open source computer vision library,直译为中文便是开源计算机视觉库。这是一个跨平台的计算机视觉库，最初由英特尔公司发起并参与开发，以BSD许可证授权发行，可以在商业和研究领域中免费使用。opencv可用于开发实时的图像处理、计算机视觉以及模式识别的应用程序，该程序库也是支持opencl和英特尔IPP进行加速。

opencv是开源的，浏览和下载源代码可以到官网和github。这里给出地址

- [opencv官网](https://opencv.org/)
- [opencv的github仓库](https://github.com/opencv/opencv)


## 起源
OpenCV的历史最早要追溯到1999年，由英特尔公司发起。最开始是致力于CPU的密集型任务，当时只是包含于光线追踪和3D显示计划中的一部分。早期OpenCV的主要目标是
	

 1. 为推进机器视觉的研究提供一套开源且优化的基础库，不重复造轮子。
 2. 提供一个共同的代码库，使得开发人员的代码更易阅读和转让，促进知识的传播。
 3. 通过提供一个不需要开源的或者免费的软件许可，促进商业软件应用的开发。
## 发展历程
OpenCV从上个世纪末开始，历经几十年的发展，期间发布了大大小小几十个版本。截至目前（2019年06月15日），从github的opencv项目代码仓库上可以查看到有81个发布。
![opencv-github-release.png][1]

但由于opencv诞生得比github早，在github的代码仓库上最早可以找到代码是2010年发布的。

[opencv在github的发布地址](https://github.com/opencv/opencv/releases)

![opencv-github-release-info.png][2]
转而去opencv的官网，官网留存的代码也最早也是2011年的。

[opencv官网发布地址](https://opencv.org/releases.html)

![opencv-official-release.png][3]
总的来说opencv的发展大概是经历了四个大版本的更新和变化。简单的时间线可以总结到如下：

- 1999
OpenCV项目最早由英特尔公司于1999年启动，致力于CPU密集型的任务，是一个包括如光线追踪和3D显示的计划的一部分。

- 2000
OpenCV的第一个预览版本于2000年在IEEE Conference on Computer Vision and Pattern Recognition公开，并且后续提供了五个测试版本。

- 2006
1.0版本于2006年发布，此时的opencv接口为c语言。

- 2009
OpenCV的第二个主要版本是2009年10月的OpenCV 2.0。该版本的主要更新包括C++接口，更容易、更类型安全的模式，新的函数，以及对现有实现的优化（特别是多核心方面）。

- 2015
发布OpenCV3.0。也现在主要使用的版本。相比于2.0更改了项目的架构，更加地模块化，拥有大量的module，更偏向c++靠近，支持更多的硬件加速和更多的语言接口等。

- 2018年12月
发布4.0。主要是增强了对深度学习的支持以及全面支持c++11的新标准。

如今的opencv大约每6个月就会发布一次版本更新，想要更详细的了解opencv发布历史，可到官网链接查阅。

## 特点
作为一个历史悠久、社区庞大的开源库，opencv具备了很多优秀的特点。

- 跨平台
opencv从最开始就是致力于创造一个通用的基础视觉库，如今的opencv不仅可以使用在Windows、Linux和MacOs上，也可以在安卓、ios、以及web上使用。更为方便的是还提供了python、java等c++以的api接口。
- 高性能
opencv的编写语言为c/c++，得益于c/c++的高效率，加上代码质量也非常的高，opencv在性能上的表现是非常可观的，对比matlab等其他视觉库，在工业界上的实时性要好不少。
- 开源免费
不仅是开源，而且还是免费的，即便商用后不公开代码也不会受影响。
- 社区庞大
作为历史悠久、使用最广泛的计算机视觉库，加之近些年来计算机视觉以及深度学习的火爆，使得opencv更加活跃。
- 更新速度快
如今的opencv不仅是接受全世界开源贡献者的代码，更有者专业的团队在更新和维护着。大约每6个月就有一次大版本的更新。

## 应用领域
opencv广泛应用在各个行业领域，卫星遥感、医学图像、安防监控、面向工业的机器视觉、无人机还有军事应用等等。

文章由芒果浩明发布，转载需注明来源。
本文链接：https://mangoroom.cn/opencv/opencv-overview.html

  [1]: https://mangoroom.cn/usr/uploads/2019/06/2173217119.png
  [2]: https://mangoroom.cn/usr/uploads/2019/06/1845801143.png
  [3]: https://mangoroom.cn/usr/uploads/2019/06/1126084226.png