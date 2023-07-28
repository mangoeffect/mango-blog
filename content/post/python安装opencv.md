---
title: "python安装opencv"
categories: [ "opencv" ]
tags: [ "opencv","图像处理","计算机视觉","python","conda" ]
draft: false
slug: "python-install-opencv"
date: "2019-05-18 16:05:00"
---

## 序

opencv提供了多种语言的接口，包括c++、python、js、java、c#等等。使用最多的两种接口为python与c++，一开始芒果也觉得神奇，python与c++两个相差比较大语言竟是同样热门的opencv接口。但想想一个易于编程实现适合学术研究，一个为opencv原生接口高效率适合工业实际应用。下面来简单说一下python接口的opencv的安装。

![python-opencv.png][1]

这次将介绍两种python-opencv的安装方法。

## pip安装

第一种是使用pip安装的方法，pip安装的方式也是众多的python第三方程序包安装的方式。一般安装的形式为

```bash
pip install packages-name
```

安装opencv也一样，使用下面简单的一条命令也可以搞定

```bash
pip install opencv-python
```

假如需要安装特定版本的opencv,则在命令行后面补充版本号的参数即可，以安装2.4.9版本为例，安装命令为

```bash
pip install opencv-python==2.4.9
```

## conda安装

conda安装方式也非常简单，只需要将pip的安装命令修改用conda替换pip即可。但是conda最大的优点是方便包管理，要发挥它包管理的功能，正确的安装方式为

### 新建环境

新建环境名称为opencv,可以指定使用的python版本

```bash
conda create --name opencv python=3.7
```

### 激活环境

新建了一个环境之后，需要激活该环境才可以使用该环境

```bash
conda activate opencv
```

### 安装opencv

进入新建环境之后，该环境为一个独立的python环境，此时当然也可以使用pip安装opencv的方式安装，但芒果建议在conda环境下使用conda安装软件的安装方式。此时的安装命令为

```bash
conda install opencv
```

conda安装opencv的方式有多种，假如以上命令无法正常安装，可以尝试以下任一安装方式，以下方式均为芒果网上搜集所得

```bash
#来源conda官网
conda install -c conda-forge opencv 
conda install -c conda-forge/label/gcc7 opencv 
conda install -c conda-forge/label/broken opencv 
conda install -c conda-forge/label/cf201901 opencv 
#来源StackOverflow
conda install -c menpo opencv
conda install --channel https://conda.anaconda.org/menpo opencv3
```

## 尾巴

总的来说，opencv的python接口安装还是非常简单的，几乎就是一个命令行就解决的事情。不过出于建立良好的工作习惯，芒果建议采用conda的安装方式，新建一个虚拟的独立环境安装和使用opencv.

---

本文由芒果浩明发布，转载需注明来源。
本文链接：https://mangoroom.cn/opencv/python-install-opencv.html


  [1]: https://mangoroom.cn/usr/uploads/2019/05/2904793903.png