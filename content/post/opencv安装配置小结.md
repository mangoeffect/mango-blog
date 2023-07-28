---
title: "opencv安装配置小结"
categories: [ "opencv" ]
tags: [ "opencv","图像处理","计算机视觉" ]
draft: false
slug: "opencv-installation-and-setting-summary"
date: "2019-06-21 18:15:00"
---

由于opencv版本之间不通用的原因，自己多次更换安装了opencv各种版本以及各种安装的方式，就做个小结记录下来给大家分享一下。

## python接口安装

opencv提供了c++以及python的接口，很多对c++不是很熟悉的小伙伴就选择了python，两者都尝试了一下，同样的程序，两者写起来一对比感觉python确实间接好多。但是各有各的优点，这里就不加比较。python的安装方式有两种，**离线安装**和**在线安装**。两种方式安装都比较简单，这里就体会到了好的包管理安装方式是多么舒服方便。

**以下两种安装方式都是在windows下试验**

### 在线安装

之所以说安装简单，是因为如果你的电脑安装了pip,那么安装opencv只需要下面这一行命令

~~~powershell
pip install opencv-python	#安装opencv
~~~

但是直接在控制台输入以上命令安装opencv的python接口的方式是不推荐的，推荐使用anaconda的来安装opencv, **anaconda**是什么，维基百科的解释为：

> **Anaconda** 是一种[Python](https://zh.wikipedia.org/wiki/Python)语言的[免费增值](https://zh.wikipedia.org/wiki/%E5%85%8D%E8%B4%B9%E5%A2%9E%E5%80%BC) [开源](https://zh.wikipedia.org/wiki/%E5%BC%80%E6%BA%90)发行版，用于进行大规模数据处理, 预测分析, 和科学计算, 致力于简化包的管理和部署。Anaconda使用[软件包管理系统](https://zh.wikipedia.org/wiki/%E8%BD%AF%E4%BB%B6%E5%8C%85%E7%AE%A1%E7%90%86%E7%B3%BB%E7%BB%9F)*Conda*进行包管理 

而不管那么多，可以知道使用它的好处是方便安装和管理包，可以新建环境使得各个开发环境互不干扰，这里就是方便安装和管理opencv。l还有另一个优势是anaconda是跨平台，意味着这不仅仅适用在windows安装。

下载安装anaconda，关于安装anconda，可以自行谷歌或者百度搜索，也非常简单这里简单提一下anaconda的常用操作和命令。

- 环境管理

  ~~~bash
  #创建一个名字为python36的新环境，指定使用的python版本是pyton3.6
  conda create --name python36 python=3.6
  #同样的，可以这么新建我们的opencv环境,指定python为3.7版本(最新)
  conda create --name opencv python=3.7
  
  #激活某个已存在的环境
  activate python36 # for Windows
  source activate opencv # for Linux & Mac
  #激活后，终端就会进入相应的pyton交互环境
  # 此时，再次输入
  python --version
  # 就可以看到相应的环境信息
  
  # 返回默认环境使用
  deactivate python36 # for Windows
  source deactivate python36 # for Linux & Mac
  
  # 删除一个已有的环境
  conda remove --name python36 --all
  ~~~

  

- conda包管理

  ~~~bash
  #安装一个包package,如安装requests
  conda install requests
  
  #查看当前环境已经安装的包packages
  conda list
  
  #查找某个package的信息
  conda search numpy
  
  #更新package
  conda update -n 环境名 包名
  
  #删除package
  conda remove -n 环境名 包名
  
  #更新conda
  conda update conda
  #更新anaconda
  conda update anaconda
  
  #更新python
  conda update python
  ~~~

安装好anaconda之后，使用anaconda打开cmd

- 新建环境，切换环境

  ~~~bash
  conda create -n opencv python=3.6
  activate opencv
  ~~~

- 安装numpy,python中图像的容器一般由numpy的提供，不是c++的Mat容器

  ~~~bash
  conda install numpy
  ~~~

- 安装opencv

  ~~~bash
  conda install opencv-pyton #conda 环境下的命令行安装
  #如果当前环境安装了pip
  pip install opencv-python	#命令同样使用
  ~~~

### 离线安装

如果有网速问题困扰的，那么离线安装也是一个很好的方式。下载了离线的安装包，也可以拷贝到别的电脑上安装。

同样的离线安装也推荐是使用anaconda进行安装和管理。首先，我们先去这个[网站](https://www.lfd.uci.edu/~gohlke/pythonlibs/#opencv)下载离线安装包,选择自己需要的版本下载就好。
![opencv_python_whl.PNG](https://upload-images.jianshu.io/upload_images/5792197-e59ccf84501e0bf7.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果下载比较慢，我这里分享一下在百度网盘

~~~
链接: https://pan.baidu.com/s/1zkBVnFdn6eIsBSl-xTRStg 密码: 8f3g
~~~

里面是python3.7和3.6版本的都是64位

下载好安装包后同样的

- 新建环境

- 安装numpy

- 切换环境，安装whl离线包

  whl格式本质上是一个压缩包，里面包含了py文件，以及经过编译的pyd文件。使得可以在不具备编译环境的情况下，选择合适自己的python环境进行安装 

  ~~~bash
  pip install 离线包名称#离线conda install命令安装失败
  ~~~

  

### 测试程序

安装完成之后，可以使用下面程序例子进行测试，和你想的一样，读取和显示一张图片

~~~python
import cv2
import numpy as np

img =  cv2.imread('test.jpg')
cv2.imshow('test', img)
cv2.waitKey(0)
~~~

更简单验证方法就直接打开python命令交换窗口,打印出opencv的版本号

~~~bash
Python 3.6.4 |Anaconda, Inc.| (default, Jan 16 2018, 10:22:32) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> print(cv2.__version__)
3.4.1
>>>
~~~

## c++接口安装

至于c++接口的安装就相对复杂一点了，Windows下的安装流程大致是这么回事，一是得到opencv的头文件（include文件夹里面）、库文件（lib文件夹里面）、可执行文件(bin文件夹里面)。然后在vs工程项目下配置好三种文件。对于第一步得到三种文件，一种来源就是直接用别人编译好的，另一种就是自行编译。

### 官方安装程序安装

从官方下载程序安装就是属于第一种，从官方下载的.exe安装安装程序其实就是一个自解压程序，点击安装后可以在安装的路径查看，就include、lib和bin文件夹是配置所需的，其他文件就是源码和文档之类的。

[官网安装程序下载地址](https://opencv.org/releases.html)

![opencv_release_pack.PNG](https://upload-images.jianshu.io/upload_images/5792197-40b310d9051dcbe7.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 自编译安装

自编译安装就属于第二种了，自己下载好opencv的源代码，然后构建opencv的vs工程，编译得到opencv的头文件（include文件夹里面）、库文件（lib文件夹里面）、可执行文件(bin文件夹里面)三种文件

[自行编译代码地址](https://github.com/opencv/opencv/releases)

![opencv_release_pack2.PNG](https://upload-images.jianshu.io/upload_images/5792197-63d8d44d7bf05767.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### contrib模块安装

opencv3把原来的库代码一分为二，把一部分试验性的代码放到到了contrib模块里面。这部分代码稳定性不够或者有些涉及专利问题，需要使用opencv全部的模块功能就必须连同contrib一起编译。

[contib源码地址](https://github.com/opencv/opencv_contrib/releases)

关于自行编译openc可以参考[vs2017+cmake编译opencv3.4.1](https://ihaoming.top/archives/3ff154c4.html)

## c++接口vs配置

windows下使用vs搭建opencv开发环境是比较方便的，作为最好的IDE,使用熟练的开发效率也非常的高

### 配置库相关解释

在vs的c++工程中，需要使用第三方库的时候就在属性页里面进行相关的配置，属性页配置项非常多，但是也不需要完全掌握，只需要配置包含目录、库目录、附加依赖项，就可以使用opencv的库代码了。

#### 环境变量

#### include目录

在其中搜索源代码中所引用的包含文件的目录。 对应于 **include** 环境变量。 即opencv安装目录里面的include文件夹。

#### bin目录

在其中搜索可执行文件的目录。 对应于 **PATH** 环境变量 。注意区分32位和64位，配置环境变量的时候可以两个都填上，这样可以调试的时候自行选择32位还是64位编译调试。

#### lib目录

在其中搜索库 (.lib) 文件的目录；其中包括运行库。 对应于 **lib** 环境变量。 该设置不适用于 .obj 文件；若要链接到 .obj 文件，请在“链接器”->**“常规”**属性页中，选择**“其他库依赖项”**，然后指定文件的相对路径。 即对opencv安装目录下的lib目录，注意区分位数。

##### 官方编译lib

使用官方的自解压安装包安装后，安装目录下lib文件夹下包含着的文件，以.lib格式 结尾。opencv2二是一系列的lib文件，opencv3官方则打包成了两个lib文件，使得配置起来更加方便。

##### 自编译lib

自行编译所得的lib文件，一般没有打包成两个lib文件，则无论opencv2还是opencv3都是一系列的lib文件，配置的时候需要复制填写文件名，一一复制效率太低，批量复制文件名可以参考[windows批量获取目录下的文件名](https://ihaoming.top/archives/459f009e.html)

#### 附加依赖项

附加依赖项对应lib目录文件夹里面的一系列lib文件，配置的时候一并填上既可。

#### 调试平台

调试平台按位数分为32位和64位两种，按调试模式分debug和release。所以有四种组合，每种组合有各自的属性表配置，所以使用那种调试平台就相应配置好属性表。需要注意的是环境变量配置环节，bin目录配置，32位（x86）和64位（x64）对应的是调试平台的32位和64位，与操作系统的位数无关。

### 一次简单配置

配置流程其实也不复杂

- 1、配置好环境变量，将opencv目录下bin目录添加到系统环境变量的path里面，多个则使用英文分号隔开
- 2、新建工程，新建属性表，编辑属性表
- 3、配置包含目录，填写opencv目录下include文件夹路径
- 4、配置库目录，填写opencv目录下lib文件夹路径
- 5、配置附加依赖项，填写lib文件夹下的lib文件名



例子可以参考[vs2017+OpenCV3.4.1配置](https://ihaoming.top/archives/1ca6b62d.html)

### 永久配置

关于永久配置，个人觉得比较好的方式就是，在第一次配置的时候，选择新建属性表的方式区配置，而不是直接在原工程的属性表配置，配置完成之后再把新建的属性表（在工程目录目录下面）保存到opencv安装的目录下面，这样以后新建工程直接引用第一次保存的属性表就可以，这样比较方便而且使用同时安装了不一样版本的opencv。而由于不一样的调试方式对应不一样的属性配置，所以每个调试方式应当对应着一张属性表，对应一个属性文件，则保存新建属性页的文件命名建议取有意义的名称。可以参考：

~~~c++
opencv3413_debug_x64_props	//opencv3.4.1.3 debug|x64 调试平台
opencv2416_release_win32_props	//opencv2.4.1.6 release|win32 调试平台
~~~



例子可以参考[vs2017+OpenCV3.4.1配置](https://ihaoming.top/archives/1ca6b62d.html)

### 测试程序

一切都配置好之后，可以使用下面简单的程序测试是否配置成功。这是一个读取并且显示一张图片的程序，新建好工程，保存测试图像在工程目录下面，调试程序，如果正常显示图片，则配置成功。

~~~c++
#include<opencv2/opencv.hpp>
#include<iostream>

int main()
{
    cv::Mat img = cv::imread("test.jpg");
    if(img.empty())
    {
        std::cout<<"can't load the image!"<<std::endl;
        return -1;
    }
    cv::imshow("test", img);
    cv::waitKey(0);
    return 0;
}
~~~

--- 

本文有芒果浩明发布，转载请注明链接。
本文链接：https://mangoroom.cn/opencv/opencv-installation-and-setting-summary.html