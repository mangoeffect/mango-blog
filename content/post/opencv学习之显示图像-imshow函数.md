---
title: "opencv学习之显示图像-imshow函数"
categories: [ "opencv" ]
tags: [ "opencv","c++","imshow" ]
draft: false
slug: "opencv-learning-imshow"
date: "2019-05-23 18:50:00"
---

## 序

上一篇[opencv学习之读取图像-imread函数](https://mangoroom.cn/opencv/opencv-leaning-imread.html)介绍完opencv读取图片函数imread，这次来介绍与它对应的图像显示函数imshow。

## imshow函数

### imshow函数功能

imshow的函数功能也非常简单，名称也可以看出来，image show的缩写。imshow负责的就是将图片显示在窗口中，通过设备屏幕展现出来。与imread一样，在matlab中也有一个相同功能的函数命名为imshow, 这也是opencv借鉴了matlab的命名，在早期opencv1.x的版本中，负责显示图像的功能的函数为cvShowImage。

### imshow函数原型

以下分别为imshow函数的c++与python原型。

**imshow函数c++原型**

```cpp
#include <opencv2/highgui.hpp>//所在头文件
void cv::imshow	(	const String & 	winname,
InputArray 	mat 
)		
```

**imshow函数python原型**

```python
Python:
None	=	cv.imshow(	winname, mat	)

```

函数的原型非常地简单，可以很容易理解

- 没有返回值
- 参数1， 显示的窗口名， 可以使用cv::namedWindow函数创建窗口，如不创建，imshow函数将自动创建。
- 需要显示的图像



### imshow函数使用示例

```cpp
#include<iostream>

#include<opencv2/opencv.hpp>

using namespace cv;

using namespace std;

int main()

{

    //read the image

    Mat image = imread("./clock.jpg");

    if (image.data != NULL)

    {

        //show the image

        imshow("clock", image);

        // imshow之后必须有waitKey函数，否则显示窗内将一闪而过，不会驻留屏幕
        waitKey(0);

    }

    else

    {

        cout << "can&apos;t openc the file!" << endl;

        getchar();

    }

    return 0;

}
```

使用方法也非常简单，需要注意的一点就是，**imshow之后必须有waitKey函数，否则显示窗内将一闪而过，不会驻留屏幕。**

## 尾巴

imshow显示图片的窗口非常地简单，实际的开发中也不会有人使用imshow去做实际的开发，都是使用第三方的UI控件去显示图像，但不代表imshow没什么作用，在调试和试验的时候还是非常方便的。

---

本文由芒果浩明发布，转载需注明来源。
本文链接：https://mangoroom.cn/opencv/opencv-learning-imshow.html
