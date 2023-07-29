---
title: "opencv-python程序包简介"
categories: [ "opencv" ]
tags: [ "opencv","图像处理","计算机视觉","python" ]
draft: false
slug: "opencv/opencv-python-introduction"
date: "2019-05-18 17:48:00"
---

## 序

上一篇在[【python安装opencv】](https://mangoroom.cn/opencv/python-install-opencv.html)中芒果已经介绍了如何安装python的opencv程序包，安装也非常简单，几乎是一条命令行的工作。这得益于python非常好第三方程序包管理，本文就介绍opencv-python这一程序包。

![图1:opencv-python](https://mangoroom.cn/usr/uploads/2019/05/2904793903.png)
## 概要

opencv-python程序包是发布在PyPi网站上的一个opencv的python接口程序包。这个包的详细信息地址为[【opencv-python地址】](https://pypi.org/project/opencv-python/),它主要有以下的特点

- 以wheel方式打包的，包的格式为***.whl**
- **非官方**，这个包并非opencv官方发布的，opencv官方团队并未在PyPi发布python的接口包此程序包由第三方编译打包发布，作者为[skvark](https://pypi.org/user/skvark/)。
- 项目的**github**地址为[【opencv-python的github地址】](https://github.com/skvark/opencv-python)

所以可以知道，此程序包简单的情况为这是一个.whl格式的python程序包，而且是非官方发布的，可以在github上面查看项目。虽然是非官方发布的程序包，但因为使用的忍术 特别多，项目更新速度非常快，紧跟官方的版本。所以体验与使用官方的源码自行编译的基本没什么区别，芒果也曾一度认为这是官方发布的程序包。

## 注意

以下是安装以及使用该程序包需要注意的一些点，关于安装的说明也可以参考[【python安装opencv】](https://mangoroom.cn/opencv/python-install-opencv.html)一文

- 如果您安装了以前/其他手动安装（=未通过pip安装）版本的OpenCV（例如Python的站点包的根目录中的cv2模块），请在安装前将其删除以避免冲突。

- 根据你的环境选择正确的程序包，因为有四种不同的包，你应该只选择其中一种。不要在同一环境中安装多个不同的软件包。因为它们之间没有插件架构，都使用相同的名字空间cv2,所以为了避免冲突只能选择安装其中一个版本。四种包分别为

  ```
  #a 适用于桌面系统的（带有GUI组件）
  pip install opencv-python#如果你只需要安装opencv主仓库的模块
  pip install opencv-contrib-python#如果您需要main模块和contrib模块，具体可以查看opencv的文档说明
  
  #b 服务器（无头）环境的包
  #这些包不包含任何GUI功能。它们更小，适用于更受限制的环境。
  #相同地有两个仓库
  pip install opencv-python-headless
  pip install opencv-contrib-python-headless
  ```

- 导入包和使用

  ```python
  import cv2#只需一行
  ```

- **不需要再另外地安装opencv**，这些包是特殊的轮二进制包，它们已经包含静态构建的OpenCV二进制文件。

- 安装失败一般可能与pip的版本太久有关，可以尝试通过升级pip解决

  ```bash
  pip install --upgrade pip
  ```

- 在windows上导入和使用失败，如果在Windows上导入失败，请确保已安装V[Visual C++ redistributable 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48145)。如果您使用的是比Windows 10更旧的Windows版本并且未安装最新的系统更新，则可能还需要[Universal C Runtime](https://support.microsoft.com/en-us/help/2999226/update-for-universal-c-runtime-in-windows)。

## 尾巴

以上简单介绍了opencv-python这一程序包，可以了解到opencv-python的**主要信息**以及使用**注意事项**。芒果认为要特别注意的点是四个版本的区别以及它是非官方的，详情了解可以前往它github的项目地址[【opencv-python的github地址】](https://github.com/skvark/opencv-python)

---

本文由芒果浩明发布，转载需注明来源。
本文链接：https://blog.mangoroom.cn/opencv/opencv-python-introduction.html
