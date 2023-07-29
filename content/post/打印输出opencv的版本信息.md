---
title: "打印输出opencv的版本信息"
categories: [ "opencv" ]
tags: [ "opencv","版本" ]
draft: false
slug: "opencv/print-opencv-version-info"
date: "2019-06-27 10:59:00"
---

## 序

查看自己安装的opencv的版本信息的方法有两种。

### 方法一-查看lib文件
到opencv的安装目录。进去lib文件夹，查看opencv的lib文件名称，文件名称中含有的数字便是opencv的版本信息。例如

```
opencv_calib3d411d.lib
opencv_core411d.lib
opencv_dnn411d.lib
opencv_features2d411d.lib
opencv_flann411d.lib
opencv_gapi411d.lib
opencv_highgui411d.lib
opencv_imgcodecs411d.lib
opencv_imgproc411d.lib
opencv_ml411d.lib
opencv_objdetect411d.lib
opencv_photo411d.lib
opencv_stitching411d.lib
opencv_video411d.lib
opencv_videoio411d.lib
```
以上的opencv版本就是opencv4.1.1,版本号接着的字母d是debug的意思，表示这是debug版的opencv.

### 方法二-代码输出

除了方法一查看lib文件的方法，还可以以代码输出的方式。输出的代码如下：

```c++
#include<iostream>
#include<opencv2/opencv.hpp>


int main()
{
	std::cout << "OpenCV version : " << CV_VERSION << std::endl;
	std::cout << "Major version : " << CV_MAJOR_VERSION << std::endl;
	std::cout << "Minor version : " << CV_MINOR_VERSION << std::endl;
	std::cout << "Subminor version : " << CV_SUBMINOR_VERSION << std::endl;

	system("pause");
	return 0;
}
```

运行后，芒果测试的输出结果为：

```
OpenCV version : 4.1.1-pre
Major version : 4
Minor version : 1
Subminor version : 1
Press any key to continue . . .
```
可以看到，以上的信息。芒果使用的opencv是opencv-4.1.1预览版的（芒果自编译），主要版本4，次要版本为1，次要子版本为1。

---

打印版本信息的代码非常简单，只需要输出定义的版本的几个宏，芒果顺便查阅了官方文档，几个宏的定义如下：

```
§ CV_MAJOR_VERSION
#define CV_MAJOR_VERSION   CV_VERSION_MAJOR
§ CV_MINOR_VERSION
#define CV_MINOR_VERSION   CV_VERSION_MINOR
§ CV_SUBMINOR_VERSION
#define CV_SUBMINOR_VERSION   CV_VERSION_REVISION
§ CV_VERSION
#define CV_VERSION   CVAUX_STR(CV_VERSION_MAJOR) "." CVAUX_STR(CV_VERSION_MINOR) "." CVAUX_STR(CV_VERSION_REVISION) CV_VERSION_STATUS
```
可以看到，与版本信息相关的几个宏定义都有两个以上的值，意味着输出CV_MAJOR_VERSION与CV_VERSION_MAJOR是一样的。

### 利用版本信息

不同的opencv版本代码使用有所不一样，所以要写出兼容不同版本opencv的代码，这时可以利用opencv的版本信息。

```c++
if ( CV_MAJOR_VERSION >= 4)
  {
      //  opencv4.x版本代码
  } else if(CV_MAJOR_VERSION >= 3)
  {
      // opencv3.x版本代码
  }else
  {
      // opencv2.x版本代码
  }
```

## 尾巴

网上很多人喜欢用imshow函数来验证opencv是否安装和配置成功，其实输出版本信息也是一个非常简单的方法。

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/opencv/print-opencv-version-info.html