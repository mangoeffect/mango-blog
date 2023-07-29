---
title: "opencv学习之读取图像-imread函数"
categories: [ "opencv" ]
tags: [ "opencv","c++","imread" ]
draft: false
slug: "opencv/opencv-learning-imread"
date: "2019-05-22 12:37:00"
---

## 序

想要完整全面地学习opencv,仅凭阅读samples的示例源码是不够的。毕竟opencv是一个拥有非常多函数的程序库，所以在每学习一个函数时，芒果觉得有必要记录下来，分享给有需要的同学。于是，就有了这一篇的开始，以后的这个就归为opencv函数学习的系列了，篇幅应该都会比较短。

## imread函数

### imread函数作用

imread函数的作用非常简单，从函数的名称也可以看出来，imread为image read的缩写，即图像读取的意思，。那么imread函数的作用就很明显了，负责读取图像。其实学过matlab的同学就会知道，matlab中也有一个读取图像的函数也命名为imread，这是opencv借鉴了matlab而命名的，因为在opencv1.x时代，加载图像的函数并不叫imread,二是由cvLoadImage函数负责。

### imread函数原型

话不多说，先了解imread函数的原型，从opencv的最新document可以查阅到imread原型为

#### imread c++原型
```cpp
#include <opencv2/imgcodecs.hpp>
Mat cv::imread	(	const String & 	filename,
int 	flags = IMREAD_COLOR 
)		

```

#### imread python原型
```python
Python:
retval	=	cv.imread(	filename[, flags]	)
```

可以看到，imread函数原型非常简单，可以总结为三点

- 返回值,Mat 类型， 即返回读取的图像，读取图像失败时返回一个空的矩阵对象（Mat::data == NULL)
- 参数1 filename, 读取的图片文件名，可以使用相对路径或者绝对路径，但必须带完整的文件扩展名（图片格式后缀）
- 参数2 flags, 一个读取标记，用于选择读取图片的方式，默认值为IMREAD_COLOR，flag值的设定与用什么颜色格式读取图片有关

**参数1 补充：**

imread函数支持读取的图像格式有

- Windows bitmaps - *.bmp, *.dib (always supported)
- JPEG files - *.jpeg, *.jpg, *.jpe (see the Note section)
- JPEG 2000 files - *.jp2 (see the Note section)
- Portable Network Graphics - *.png (see the Note section)
- WebP - *.webp (see the Note section)
- Portable image format - *.pbm, *.pgm, *.ppm *.pxm, *.pnm (always supported)
- PFM files - *.pfm (see the Note section)
- Sun rasters - *.sr, *.ras (always supported)
- TIFF files - *.tiff, *.tif (see the Note section)
- OpenEXR Image files - *.exr (see the Note section)
- Radiance HDR - *.hdr, *.pic (always supported)
- Raster and Vector geospatial data supported by GDAL (see the Note section)
Note

**参数2 补充：**

这些flags值被定义在enum cv::ImreadModes枚举类里面

| c++定义 | python定义 | 说明 |
| :----: | :----: | :---: |
|IMREAD_UNCHANGED |Python: cv.IMREAD_UNCHANGED |如果设置，则按原样返回加载的图像（使用Alpha通道，否则会被裁剪）
| IMREAD_GRAYSCALE | Python: cv.IMREAD_GRAYSCALE | 如果设置，则始终将图像转换为单通道灰度图像（编解码器内部转换）。
| IMREAD_COLOR | Python: cv.IMREAD_COLOR | 如果设置，请始终将图像转换为3通道BGR彩色图像。
| IMREAD_ANYDEPTH | Python: cv.IMREAD_ANYDEPTH| 如果设置，则在输入具有相应深度时返回16位/ 32位图像，否则将其转换为8位。
| IMREAD_ANYCOLOR | Python: cv.IMREAD_ANYCOLOR | 如果设置，则以任何可能的颜色格式读取图像。
| IMREAD_LOAD_GDAL | Python: cv.IMREAD_LOAD_GDAL| 如果设置，使用gdal驱动程序加载图像
| IMREAD_REDUCED_GRAYSCALE_2 | Python: cv.IMREAD_REDUCED_GRAYSCALE_2| 如果设置，则始终将图像转换为单通道灰度图像，图像尺寸减小1/2。
| IMREAD_REDUCED_COLOR_2 | Python: cv.IMREAD_REDUCED_COLOR_2 | 如果设置，则始终将图像转换为3通道BGR彩色图像，图像尺寸减小1/2。
| IMREAD_REDUCED_GRAYSCALE_4 | Python: cv.IMREAD_REDUCED_GRAYSCALE_4| 如果设置，则始终将图像转换为单通道灰度图像，图像尺寸减小1/4
| IMREAD_REDUCED_COLOR_4 | Python: cv.IMREAD_REDUCED_COLOR_4| 如果设置，则始终将图像转换为3通道BGR彩色图像，图像尺寸减小1/4
| IMREAD_REDUCED_GRAYSCALE_8 | Python: cv.IMREAD_REDUCED_GRAYSCALE_8| 如果设置，则始终将图像转换为单通道灰度图像，图像尺寸减小1/8。
| IMREAD_REDUCED_COLOR_8 | Python: cv.IMREAD_REDUCED_COLOR_8| 如果设置，则始终将图像转换为3通道BGR彩色图像，图像尺寸减小1/8。
| IMREAD_IGNORE_ORIENTATION | Python: cv.IMREAD_IGNORE_ORIENTATION | 如果设置，请不要根据EXIF的方向标志旋转图像。


### imread函数使用示例

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
## 尾巴

opencv的官方文档document其实是一份非常好的资料，关于opencv的几乎都可以在上面查阅得到，不过缺点就是文档为全英文的，这个需要自己克服一下。

---

本文由芒果浩明发布，转载需注明来源。
本文链接：https://blog.mangoroom.cn/opencv/opencv-learning-imread.html