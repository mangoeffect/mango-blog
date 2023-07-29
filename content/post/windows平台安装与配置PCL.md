---
title: "windows平台安装与配置PCL"
categories: [ "PCL" ]
tags: [ "vs2019","cmake","vs","pcl" ]
draft: false
slug: "PCL/install-and-configure-pcl-on-windows"
date: "2020-09-28 11:28:00"
---

本篇介绍windows平台下，PCL的安装与配置使用。PCL依赖的第三方库比较多，windows平台下从源码编译安装比较麻烦，这里采用官方编译好的all-in-one安装包。

## 下载安装包

下载链接[https://github.com/PointCloudLibrary/pcl/releases](https://github.com/PointCloudLibrary/pcl/releases)

在github仓库的release记录下可以下载各个版本的all-in-one安装包，选择自己需要的版本下载即可。建议使用迅雷下载，浏览器和其他软件访问github速度有点堪忧。

## 安装

下载的安装包是一个可执行文件，双击程序即可一步一步按照提示安装。


![install-1.png][1]
 
![install-2.png][2]

![install-3.png][3]
这一步需要注意把环境变量给勾选上，否则要自己一个一个添加。


![install-4.png][4]
![install-5.png][5]
![install-6.png][6]

这里默认安装第三方依赖库

## 配置

安装完毕后到这一步就是如何配置和使用PCL了，Window平台下c++开发普遍用的的是宇宙第一IDE Visual Studio，所以网上很多教程普遍都是介绍配置VS属性表的方式配置VS,这种方式实际上比较麻烦，PCL依赖库众多，一遍要花费的时间可以不少。这里介绍另一种比较方便的方式，基于CMake的构建方法。

首先安装好CMake，并将CMake添加至环境变量
将
```
C:\Program Files\CMake\bin\
```
添加至系统环境变量path中，并且确保安装的PCL环境变量已经添加好，做好以上准备后，咱开始构建。

首先假设你的项目有一个main.cpp文件


接着编写CMakeLists.txt文件

```cmake
# cmake 最低版本要求
cmake_minimum_required(VERSION 2.8)
# 项目名称
project(configure_pcl_on_window)

# 查找PCL相关库文件，要求PCL最低版本为1.3
find_package(PCL 1.3 )
# 头文件包含目录
include_directories(${PCL_INCLUDE_DIRS})
# 库目录
link_directories(${PCL_LIBRARY_DIRS})
# 一些PCL宏定义
add_definitions(${PCL_DEFINITIONS})

# 添加项目可执行文件,编译main.cpp
add_executable(${PROJECT_NAME} main.cpp)

# 链接PCL库文件
target_link_libraries(${PROJECT_NAME} ${PCL_LIBRARIES})
```

利用CMake,你还可以实现配置需要的PCL模块，例如


```cmake
# cmake 最低版本要求
cmake_minimum_required(VERSION 2.8)
# 项目名称
project(configure_pcl_on_window)

# 查找PCL相关库文件，要求PCL最低版本为1.3, 只需要common 与io模块
find_package(PCL 1.3 REQUIRED COMPONENTS common io)
# 头文件包含目录
include_directories(${PCL_INCLUDE_DIRS})
# 库目录
link_directories(${PCL_LIBRARY_DIRS})
# 一些PCL宏定义
add_definitions(${PCL_DEFINITIONS})

# 添加项目可执行文件,编译main.cpp
add_executable(${PROJECT_NAME} main.cpp)

# 链接PCL库文件
target_link_libraries(${PROJECT_NAME} ${PCL_LIBRARIES})
```

此时目录下面应为main.cpp与CMakeLists.txt文件

```bash
main.cpp
CMakeLists.txt
```
cd到该目录下

```bash
mkdir build
cd build/
cmake -G "Visual Studio 16 2019 Win64" ..
```
build目录下自动生成已经配置好PCL的VS工程，打开就可以直接进行编码开发了。

对关键命令做一下解释

```bash
cmake -G "Visual Studio 16 2019 Win64" .. #生成vs2019 64位的项目工程
cmake -G "Visual Studio 16 2019" .. #生成vs2019 32位的项目工程
cmake -G "Visual Studio 15 2017 Win64" .. #生成vs2017 64位的项目工程
cmake -G "Visual Studio 15 2017 " .. #生成vs2017 32位的项目工程
```
------------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/pcl/install-and-configure-pcl-on-windows.html

  [1]: https://mangoroom.cn/usr/uploads/2020/09/2952299226.png
  [2]: https://mangoroom.cn/usr/uploads/2020/09/2994108311.png
  [3]: https://mangoroom.cn/usr/uploads/2020/09/1695560215.png
  [4]: https://mangoroom.cn/usr/uploads/2020/09/1970774546.png
  [5]: https://mangoroom.cn/usr/uploads/2020/09/4049375671.png
  [6]: https://mangoroom.cn/usr/uploads/2020/09/3160792180.png