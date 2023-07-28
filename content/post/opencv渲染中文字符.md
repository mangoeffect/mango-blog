---
title: "opencv渲染中文字符"
categories: [ "opencv","c++","计算机视觉" ]
tags: [ "opencv","图像处理","计算机视觉","渲染","puttext" ]
draft: false
slug: "opencv-puttext-chinese-characters"
date: "2019-11-09 11:20:00"
---

## 目标

读完本篇笔记你可以知道以下几个问题。

- opencv为何不支持往图片渲染中文字符。
- 有哪几种方法可以实现opencv对中文字符的支持。

![中文渲染.png][1]
## opencv不支持渲染中文字符的原由

opencv不支持渲染中文字符的本质是不支持对utf-8的字符渲染，opencv库中的```cv::putText()```函数仅支持对ascii码，这是一个很小的字符编码，想要支持中文或者其他字符的渲染就需要支持Unicode的字符集。其实早期的opencv是字符Unicode的字符渲染的，在c语言版本时代采用的是FreeType库实现的，但由于FreeFype是GPL版权发布的库，和OpenCV版权并不一致，因此目前还没有合并到OpenCV。

## 三种方式实现opencv对中文字符的渲染

现在已经知道opencv不支持渲染中文的原因，那么只需要解决opencv支持Unicode字符集的渲染即可。想要实现opencv对Unicode字符集的支持，可以通过以下三种方式。

## （1）使用freetype库
这是原先opencv采用的方法，所以理论上我们可以通过自行添加freetype库来实现opencv渲染中文字符。实现方法步骤如下

### （1.1）编译freetype库

首先，下载freetype库，下载地址

[http://download.savannah.gnu.org/releases/freetype/](http://download.savannah.gnu.org/releases/freetype/)

下载完成后解压，打开build文件夹，选择合适的vs解决方案进行编译，编译后会得到静态链接库文件。如图
![freetype编译.png][2]

### （1.2）添加freetype到工程并且使用

添加freetype到工程的配置过程与opencv类似，一是把include添加到包含目录，实际就是添加头文件，二是将lib文件目录添加到库目录并且将名称填写到附加依赖项。
**配置的过程需要注意的点是配置的freetype库与项目工程对应，如x86与x64，debug与release。**

使用的方法非常简单，参考如下的例子：

```c++
#include "opencv2/opencv.hpp"
#include "CvxText.h" 
using namespace cv;
 
int main(int argc, char *argv[])
{
    Mat img = imread("lena.jpg", 0);

    //1.载入希望输入的字体，可在目录C:\WINDOWS\Fonts中选择相应字体文件，并复制到工程下
    CvxText text("msyh.ttf");	
    
    //2.设置字体的大小/空白比例/间隔比较/旋转角度
    Scalar size(15, 0.5, 0.1, 0);	
    float p = 1.f; //设置字体的透明度
    const char *msg = "opencv中文渲染";
    
    // 设置字体
    text.setFont(nullptr, &size, nullptr, &p);

    //3. 渲染到图像
    text.putText(img, msg, Point(140, 250), CV_RGB(0,255,0));
    
    imwrite("res.bmp", img);
    
    return 0;
}
```

## （2）编译opencv勾选with Qt

第二种方法是编译opencv的时候，勾选wiht Qt的选项，因为Qt的渲染是支持Unicode字符集的，所以在编译opencv的时候联合编译qt的部分代码可以实现对Unicode的支持。将qt添加到opencv里一起编译本是一件比较麻烦的事，所幸的是opencv官方已经替我们解决了，已经将此选项写在了cmake的构建文件里。所以在编译的时候只需要我们勾选即可。而且在qt的官网可以找到编译教程，教程地址：
[https://wiki.qt.io/How_to_setup_Qt_and_openCV_on_Windows](https://wiki.qt.io/How_to_setup_Qt_and_openCV_on_Windows)。

---

简单地说，参考教程，只需要在编译opencv的时候，勾选以下两个选项即可
```
Check the box [X]WITH_QT
Check the box [X]WITH_OPENGL
```
编译完成后，配置opencv到工程中，渲染的方法有所不同了，使用的是qt的一个函数。使用例子如下

```c++
#include "opencv2/opencv.hpp"

using namespace cv;
 
int main(int argc, char *argv[])
{
    Mat img = imread("lena.jpg");
  
    cv::addText(img, "中文测试", cv::Point(100, 50), cv::fontQt("Times"));
    
    imwrite("res.bmp", img);
    
    return 0;
}
```

该函数的原型如下

```c++
void cv::addText(	
const Mat & 	img,
const String & 	text,
Point 	org,
const QtFont & 	font 
)	
```

```c++
void cv::addText	(	const Mat & 	img,
const String & 	text,
Point 	org,
const String & 	nameFont,
int 	pointSize = -1,
Scalar 	color = Scalar::all(0),
int 	weight = QT_FONT_NORMAL,
int 	style = QT_STYLE_NORMAL,
int 	spacing = 0 
)		
```

## （3）重写putText函数自行实现

最后一种方法是可以自己实现绘制函数

- 头文件

```c++
#pragma once
#include <windows.h>
#include <string>
#include "include_opencv.h"

using namespace cv;

void GetStringSize(HDC hDC, const char* str, int* w, int* h);
void putTextZH(Mat dst, const char* str, Point org, Scalar color, int fontSize,
	const char *fn = "Arial", bool italic = false, bool underline = false);

void putTextZh(InputOutputArray 	img,
	const char *text,
	Point 	org,
	int     fontSize,
	Scalar 	color,
	const char *font = "Arial",
	bool italic = false,
	bool underline = false);
```

- 源文件

```c++
#include"puttext_zh.h"

void GetStringSize(HDC hDC, const char* str, int* w, int* h)
{
	SIZE size;
	GetTextExtentPoint32A(hDC, str, strlen(str), &size);
	if (w != 0) *w = size.cx;
	if (h != 0) *h = size.cy;
}

void putTextZH(Mat dst, const char* str, Point org, Scalar color, int fontSize, const char* fn, bool italic, bool underline)
{
	CV_Assert(dst.data != 0 && (dst.channels() == 1 || dst.channels() == 3));

	int x, y, r, b;
	if (org.x > dst.cols || org.y > dst.rows) return;
	x = org.x < 0 ? -org.x : 0;
	y = org.y < 0 ? -org.y : 0;

	LOGFONTA lf;
	lf.lfHeight = -fontSize;
	lf.lfWidth = 0;
	lf.lfEscapement = 0;
	lf.lfOrientation = 0;
	lf.lfWeight = 5;
	lf.lfItalic = italic;   //斜体
	lf.lfUnderline = underline; //下划线
	lf.lfStrikeOut = 0;
	lf.lfCharSet = DEFAULT_CHARSET;
	lf.lfOutPrecision = 0;
	lf.lfClipPrecision = 0;
	lf.lfQuality = PROOF_QUALITY;
	lf.lfPitchAndFamily = 0;
	strcpy_s(lf.lfFaceName, fn);

	HFONT hf = CreateFontIndirectA(&lf);
	HDC hDC = CreateCompatibleDC(0);
	HFONT hOldFont = (HFONT)SelectObject(hDC, hf);

	int strBaseW = 0, strBaseH = 0;
	int singleRow = 0;
	char buf[1 << 12];
	strcpy_s(buf, str);
	char *bufT[1 << 12];  // 这个用于分隔字符串后剩余的字符，可能会超出。
	//处理多行
	{
		int nnh = 0;
		int cw, ch;

		const char* ln = strtok_s(buf, "\n", bufT);
		while (ln != 0)
		{
			GetStringSize(hDC, ln, &cw, &ch);
			strBaseW = max(strBaseW, cw);
			strBaseH = max(strBaseH, ch);

			ln = strtok_s(0, "\n", bufT);
			nnh++;
		}
		singleRow = strBaseH;
		strBaseH *= nnh;
	}

	if (org.x + strBaseW < 0 || org.y + strBaseH < 0)
	{
		SelectObject(hDC, hOldFont);
		DeleteObject(hf);
		DeleteObject(hDC);
		return;
	}

	r = org.x + strBaseW > dst.cols ? dst.cols - org.x - 1 : strBaseW - 1;
	b = org.y + strBaseH > dst.rows ? dst.rows - org.y - 1 : strBaseH - 1;
	org.x = org.x < 0 ? 0 : org.x;
	org.y = org.y < 0 ? 0 : org.y;

	BITMAPINFO bmp = { 0 };
	BITMAPINFOHEADER& bih = bmp.bmiHeader;
	int strDrawLineStep = strBaseW * 3 % 4 == 0 ? strBaseW * 3 : (strBaseW * 3 + 4 - ((strBaseW * 3) % 4));

	bih.biSize = sizeof(BITMAPINFOHEADER);
	bih.biWidth = strBaseW;
	bih.biHeight = strBaseH;
	bih.biPlanes = 1;
	bih.biBitCount = 24;
	bih.biCompression = BI_RGB;
	bih.biSizeImage = strBaseH * strDrawLineStep;
	bih.biClrUsed = 0;
	bih.biClrImportant = 0;

	void* pDibData = 0;
	HBITMAP hBmp = CreateDIBSection(hDC, &bmp, DIB_RGB_COLORS, &pDibData, 0, 0);

	CV_Assert(pDibData != 0);
	HBITMAP hOldBmp = (HBITMAP)SelectObject(hDC, hBmp);

	//color.val[2], color.val[1], color.val[0]
	SetTextColor(hDC, RGB(255, 255, 255));
	SetBkColor(hDC, 0);
	//SetStretchBltMode(hDC, COLORONCOLOR);

	strcpy_s(buf, str);
	const char* ln = strtok_s(buf, "\n", bufT);
	int outTextY = 0;
	while (ln != 0)
	{
		TextOutA(hDC, 0, outTextY, ln, strlen(ln));
		outTextY += singleRow;
		ln = strtok_s(0, "\n", bufT);
	}
	uchar* dstData = (uchar*)dst.data;
	int dstStep = dst.step / sizeof(dstData[0]);
	unsigned char* pImg = (unsigned char*)dst.data + org.x * dst.channels() + org.y * dstStep;
	unsigned char* pStr = (unsigned char*)pDibData + x * 3;
	for (int tty = y; tty <= b; ++tty)
	{
		unsigned char* subImg = pImg + (tty - y) * dstStep;
		unsigned char* subStr = pStr + (strBaseH - tty - 1) * strDrawLineStep;
		for (int ttx = x; ttx <= r; ++ttx)
		{
			for (int n = 0; n < dst.channels(); ++n) {
				double vtxt = subStr[n] / 255.0;
				int cvv = vtxt * color.val[n] + (1 - vtxt) * subImg[n];
				subImg[n] = cvv > 255 ? 255 : (cvv < 0 ? 0 : cvv);
			}

			subStr += 3;
			subImg += dst.channels();
		}
	}

	SelectObject(hDC, hOldBmp);
	SelectObject(hDC, hOldFont);
	DeleteObject(hf);
	DeleteObject(hBmp);
	DeleteDC(hDC);
}

void putTextZh(InputOutputArray 	img,
	const char *text,
	Point 	org,
	int 	fontSize,
	Scalar 	color,
	const char  *font,
	bool italic,
	bool underline)
{
	Mat src = img.getMat();

	putTextZH(src, text, org, color, fontSize, font, italic, underline);
}
```

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://mangoroom.cn/opencv/opencv-puttext-chinese-characters.html

  [1]: https://mangoroom.cn/usr/uploads/2019/11/3596229788.png
  [2]: https://mangoroom.cn/usr/uploads/2019/11/1225716949.png