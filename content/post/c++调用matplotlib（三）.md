---
title: "c++调用matplotlib（三）"
categories: [ "c++","工具" ]
tags: [ "c++","cmake","matplotlib","vs","windows" ]
draft: false
slug: "cpp/call-matplotlib-on-cpp3"
date: "2021-10-22 23:04:00"
---

## 设置python环境变量

在安装好python后，需要将python安装目录设置为系统环境变量。设置步骤为：

1. 编辑系统环境变量
2. 新建环境变量，变量名为PYTHONHOME，变量值为pythoon安装目录，默认安装时一般为一下路径

```
//该目录为python.exe所在目录
C:\Users\UserName\AppData\Local\Programs\Python\Python36-32
```

设置完毕后保存，重启电脑以确保环境变量生效。


## CMake配置工程

windows配置调用python的VS工程比较麻烦，使用CMake构建比较方便。在此之前请确保已经安装matplotlib,

```bash
pip install matplotlib
```
安装cmake,并将cmake.exe所在目录添加至path环境变量(在安装时会提示是否选择添加，注意勾选就不需要手动添加)

-----

以下面例子说明，创建工程c++文件

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
拷贝matplotlibcpp.h库文件到main.cpp同级目录，matplotlibcpp.h文件可到github下载:[matplotlibcpp](https://github.com/lava/matplotlib-cpp/blob/master/matplotlibcpp.h)，在main.cpp同级目录新建CMakeLists.txt，内容如下：

```cmake
# cmake最低版本要求
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

接下来使用cmake生成VS工程，在CMakeLists.txt同级目录下打开Powershell终端

```bash
# 创建build目录
mkdir build
# cd 到build目录
cd build
# 如果你安装的VS为2017,且python为32位
cmake -G "Visual Studio 15 2017" -A Win32 ..
# 如果python安装为64位
cmake -G "Visual Studio 15 2017" -A Win64 ..
# 如果你使用的版本位VS2019,将以上"Visual Studio 15 2017"替换位"Visual Studio 16 2019"即可
```
执行完毕后，build目录下生成对应的VS工程，使用VS打开编译即可。生成的工程是自动配置了debug与releas版本的，但默认安装并未安装python版本，所以若想debug版本使用，需要在matplotlibcpp.h文件前面添加如下定义：

```cpp
#pragma once

// Python headers must be included before any system headers, since
// they define _POSIX_C_SOURCE

//-------添加部分--------
#ifdef _DEBUG
#undef _DEBUG
//-------添加部分--------
#include <Python.h>

#define _DEBUG
#else
#include <Python.h>
#endif
//...
```
使得debug版本工程依然调用release的python包。

------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/cpp/call-matplotlib-on-cpp3.html