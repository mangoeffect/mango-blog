---
title: "opencv代替imagewatch插件的调试方式"
categories: [ "opencv","计算机视觉" ]
tags: [ "opencv","imshow","imagewatch","opengl","opencv调试","Qt" ]
draft: false
slug: "opencv-debug-by-imshow-instead-of-imagewatch"
date: "2019-11-13 11:16:00"
---

## 目标

阅读完本篇的分享笔记后，你会了解到以下几个问题：

- imagewatch是什么有何作用？
- 如何获取替代imagewatch的调试方式？
- 相比imagewatch插件新的调试方式有何优点？

## imagewatch简介

imagewatch是一个vs的插件，现如最新支持到vs2017，此插件用于在native的c++项目中调试图像数据类型，将图像数据变量可视化以方便查看其内存和渲染后的情况。由于对c++项目的支持，imagewatch常被用于调试opencv的项目。

--- 

imagewatch的下载地址为：[vs2017版](https://marketplace.visualstudio.com/items?itemName=VisualCPPTeam.ImageWatch2017)。
下载安装后重启vs即可完成安装。

### imagewatch简单上手

（1）在安装完成之后，新建一个opencv项目。按F5进入调试模式。

（2）选择菜单View->Other Windows->Image Watch,就会出现imagewatch的调试界面。

在调试界面你可以进行如下操作：
 - 查看断点附近的内存中的图像变量（imagewatch会自动显示）。
 - 选中某个在被监控的图像变量，可以浏览其渲染的显示效果，将鼠标移动到渲染的图片上，会实时得到鼠标在图像的坐标和像素值。
 - 将鼠标放置在渲染的监控图像上，可以放大和缩小渲染图片，支持放大到可以看到像素点的（R、G、B）值。

 更详细的imagewatch使用方式可以参考以下文章：

 [csdn-Visual Studio中Image Watch的使用](https://blog.csdn.net/qianqing13579/article/details/45970197)
 [opencv官方-windows_visual_studio_image_watch](https://docs.opencv.org/2.4/doc/tutorials/introduction/windows_visual_studio_image_watch/windows_visual_studio_image_watch.html)

**注意：** imagewatch仅支持在dubug模式下进行调试。


## 代替imagewatch的调试方式

相信很多人和芒果一样，在不知道imagewatch插件的时候，在学习opencv的时候，一般都是新建控制台项目，然后在项目调试过程中假如想查看某个图片经处理后是什么样子。然后，采用的方法便是最直观和简单的```cv::imshow()```将其渲染在屏幕上，这个方式其实很自由也很方便，但此方式有一个缺点，无法放大图像查看像素点的R、G、B值。那有无办法解决这个缺点呢，办法是有的而且opencv官方也已经为我们准备了解决方法。此方法就是在编译opencv的时候，勾选对qt以及opencgl的支持。以这样的方式编译得出的opencv，```cv::imshow()```函数渲染的方式与之前有所变化了，此时渲染图像的窗口是opengl绘制的，支持放大图像至像素级，可查看像素点的R、G、B值大小。


### 如何获取支持缩放的imshow

**编译所需条件：**

- 已安装Qt
- 已安装Qt for mvsc的支持工具（对应的vs版本）
- cmake
- opencv源码

---

方法很简单，qt的官方也给出了详细的教程。教程地址为：[https://wiki.qt.io/How_to_setup_Qt_and_openCV_on_Windows](https://wiki.qt.io/How_to_setup_Qt_and_openCV_on_Windows)

---

教程中关键之处就在于，勾选cmake编译选项之时，将

```
Check the box [X]WITH_QT
Check the box [X]WITH_OPENGL
```
勾选上。即在windows使用cmake的gui程序编译opencv的时候，在configuration阶段，将WITH_QT、WITH_OPENGL选项打勾选上，后重新configure至界面不标红色，然后generation解决方案，最后open project编译。


### 与imagewatch相比有何优点

- 同样支持缩放调试图像值像素级，可查看像素点的R、G、B像素值。
- 同样支持实时查看鼠标位置的像素点坐标以及像素值
- 支持imagewatch不支持的release调试。
- 更自由更方便，无需设置断点，只需在需要查看的图像变量后添加一行代码即可。
- 支持界面选择保存图像到本地

![opengl-imshow1.png][1]
![opengl-imshow2.png][2]

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://mangoroom.cn/opencv/opencv-debug-by-imshow-instead-of-imagewatch.html

  [1]: https://mangoroom.cn/usr/uploads/2019/11/322310943.png
  [2]: https://mangoroom.cn/usr/uploads/2019/11/3536942200.png