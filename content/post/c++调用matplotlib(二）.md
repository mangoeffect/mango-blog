---
title: "c++调用matplotlib(二）"
categories: [ "c++","工具" ]
tags: [ "c++","cmake","matplotlib" ]
draft: false
slug: "call-matplotlib-on-cpp-2-html"
date: "2020-09-22 20:25:00"
---

在上一篇[c++调用matplotlib](https://mangoroom.cn/cpp/call-matplotlib-on-cpp.html)文章里介绍了基于开源项目matplotlibcpp在windows平台上c++对matplotlib库进行调用，虽然成功地调用到了python绘图包matplotlib进行绘图，但仍有几个问题没解决：

- 无法在debug模式下调用
- 每次使用都需要手动配置vs项目属性
- 没安装numpy或者numpy版本不对应时出现无法正常调用的情况

针对以上问题，本篇就一一进行解决，最后实现在windows平台vsc++开发环境下比较完美地调用matplotlib。

- debug模式下调用

出现这个问题的原因不在于调用方法有问题或者配置方法不对，是因为我们安装的python与matplotlib包是release版本的，所以在debug模式下无法正常调用。解决办法就是程序编译链接的时候，无论debug模式还是release模式，都按照release模式来链接python的相关文件。具体处理很简单，只需要在matplotlibcpp.h 开头添加以下宏定义

```cpp
#pragma once

// Python headers must be included before any system headers, since
// they define _POSIX_C_SOURCE

#ifdef _DEBUG
#undef _DEBUG
#include <Python.h>

#define _DEBUG
#else
#include <Python.h>
#endif
```

其中原理为，在debug模式下，ide会自动定义_DEBUG宏，我们将其取消掉就可以可以实现debug模式也正常按照release模式编译和链接了。

- windows平台下消除numpy的影响

这个也非常简单，项目作者在代码里面其实也通过宏来区分了，我们只需要文件开头添加WITHOUT_NUMPY宏定义即可

```cpp
#define WITHOUT_NUMPY//添加此宏定义来明确告诉后续代码不使用numpy

#ifndef WITHOUT_NUMPY
#  define NPY_NO_DEPRECATED_API NPY_1_7_API_VERSION
#  include <numpy/arrayobject.h>

#  ifdef WITH_OPENCV
#    include <opencv2/opencv.hpp>
#  endif // WITH_OPENCV
```

- vs链接配置问题

习惯使用vs开发的同学在配置第三方库时都习惯采用配置vs属性表来引入第三方库，vs很强大开发效率也高。但一个项目配置还好，但如果频繁新建项目（比如在学习的时候）每次都要重新配置就比较麻烦，而且debug与release模式还需要分开配置，非常麻烦。对于这个问题当然也有一些改善的方法，比如芒果之前常用的新建一个属性表保存下来，后续直接导入使用，这样是方便了不少，但还是无法解决debug与release需要分别配置的问题，而且属性表是与保存的是库文件的绝对路径，当库文件一升级或者更换安装目录属性表就失效了。


这个问题使用cmake可以比较好的解决，把自动搜索库文件的工作交给cmake，然后让其自动生成vs工程。cmake是一个跨平台的构建工具，很多著名的跨平台开源库（如OpenCV)就是使用cmake构建的，详细了解可以百度或Google搜一下，这里介绍简单的例子。

假如你新建一个工程，工程源文件为main.cpp,需要调用matplotlib,那么你先新建main.cpp,

```cpp
//main.cpp
#include "matplotlibcpp.h"

namespace plt = matplotlibcpp;

int main() 
{
    plt::plot({1,3,2,4});
    plt::show();
    return 0;
}
```

文件include了matplotlibcpp头文件，所以你需要将matpoltlibcpp.h文件拷贝至与main.cpp同一目录下

接着新建一个CMakeLists.txt文件用于cmake构建处理。内容如下

```cmake
cmake_minimum_required(VERSION 3.7)

# 项目名称
project (cpp_call_matplotlib)

# 设置c++编译器语言标准版本11以上
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 添加python路径,这里从环境变量里面搜索，加入你没有设置PYTHONHOME系统环境变量，这里需要填入python安装目录的绝对路径
include_directories($ENV{PYTHONHOME}/include)

# numpy可以不需要
include_directories($ENV{PYTHONHOME}/Lib/site-packages/numpy/core/include)
link_directories($ENV{PYTHONHOME}/libs)

# 添加头文件定义
add_definitions(-DMATPLOTLIBCPP_PYTHON_HEADER=Python.h)

# 可执行文件
add_executable(${PROJECT_NAME} main.cpp)
```

添加PYTHONHOME环境变量方法，编辑系统环境变量，新建PYTHONHOME，值填写python安装目录，如下图
![PYTHONHOME.png][1]

如果你不想设置，那么就再上面的include_directories语句里面填写pyhon安装目录的绝对路径。

最后编译生成vs工程，现在我们的项目目录下是这样子的

```bash
ls
CMakeLists.txt
main.cpp
matplotlibcpp.h
```

新建一个build目录存放vs工程，
```bash
mkdir build
# cd进去构建
cd build/
cmake -G "Visual Studio 15 2017" ..
# cmake -G "Visual Studio 15 2017 Win64" .. #安装的64位python时生成64位vs工程
```
然后进去build目录就可以看到vs工程，打开就可以直接编译使用了。在debug模式下运行结果：
![figure1.png][2]


------------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/cpp/call-matplotlib-on-cpp-2.html

  [1]: https://mangoroom.cn/usr/uploads/2020/09/316835497.png
  [2]: https://mangoroom.cn/usr/uploads/2020/09/1706259781.png