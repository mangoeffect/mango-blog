---
title: "Lab颜色空间"
categories: [ "opencv" ]
tags: [ "opencv","图像处理","颜色空间","Lab" ]
draft: false
slug: "lab-color-space"
date: "2019-07-02 14:26:00"
---

## Lab颜色空间简介

Lab颜色空间（英文：Lab color space）是一种基于人对颜色的感觉，基于人的生理特性建立的一种颜色空间模型。它是由国际照明委员会CIE(International Commission on Illumination)在1931年制定的颜色度量国际标准的基础上建立起来的，1976年正式修改命名为CIELab。

## 参数含义

Lab与其他的颜色空间类似，含有三个通道参数，分别为L、a、b。

### L

L代表亮度，数值范围为[0,100],L为0表示黑色，L为100表示白色。

### a

a代表从绿色到红色的分量，数值范围为[-128,127]。a的数值从负数变化到正数，对应颜色从绿色变化到红色。

### b

b代表从蓝色到黄色的分量，数值范围为[-128,127]。b的数值从负数变化到正数，对应颜色从蓝色变化到黄色。

- a和b为0时都代表灰色。 

![330px-Lab_color_space.png][1]
## Lab颜色空间特性

- 设备无关

这个特性使得，在明确了颜色空间的白点（white point）后，颜色空间就能明确地确定各个颜色是如何被创建和显示的，与使用的显示介质没有关系。 

> 白点（white point），在技术文档中常被称作参考白色（reference white）或目标白色（target white），是一组三色视觉值（tristimulus values）或色度值（Chromaticity）。它被用来在图像捕获、编码和再现时定义白色。[1]白点的数值不是恒定的，需要按照使用场合的不同对它的数值做出相应的更改。例如在室内摄影时常用白炽灯来照明，而白炽灯的光线色温相对日光较低，因而呈现出偏橙的颜色。倘若在这种情况下依然使用以日光为准的白点数值来定义白色，摄影的结果就会出现偏色的问题。

这个特性的用途之一，当你想把屏幕上的RGB图片转成打印用的CMYK图片的时候，就可以先将它从RGB转成LAB，然后再把LAB图片转成CMYK模式。

- 接近人类视觉

不像RGB和CMYK色彩空间，Lab颜色被设计来接近人类视觉。它致力于感知均匀性，它的L分量密切匹配人类亮度感知。

- 色域大

Lab空间比电脑萤幕、印表机甚至比人类视觉的色域都要大，表示为Lab的位图比RGB或CMYK位图获得同样的精度要求更多的每像素数据。

## Lab颜色空间用途

- 在Adobe Photoshop中，图象编辑使用的“Lab模式”是CIELAB D50。
- 在ICC Profile中，用做配置文件连接空间的“Lab色彩空间”是CIELAB D50。
- 在TIFF文件中，可以使用CIELAB色彩空间。
- 在PDF文档中，“Lab色彩空间”是CIELAB。

## opencv中Lab的应用

opencv中提供了Lab颜色空间的应用，如与RGB颜色空间之间的转换

```c++
// rgb转lab
 cv::cvtColor(bgr_img, lab_img, cv::COLOR_BGR2Lab);
// lab转rgb
 cv::cvtColor(lab_img, bgr_img, cv::COLOR_Lab2BGR);
```

### opencv中何时选用lab颜色空间

- 利用接近人类视觉的特性，在仅需要调整亮度的时候可以先转为lab空间，进行处理，最后再转回rgb。


reference

[维基百科-Lab色彩空间](https://zh.wikipedia.org/wiki/Lab%E8%89%B2%E5%BD%A9%E7%A9%BA%E9%97%B4)


---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://mangoroom.cn/opencv/lab-color-space.html

  [1]: https://mangoroom.cn/usr/uploads/2019/07/2930894543.png