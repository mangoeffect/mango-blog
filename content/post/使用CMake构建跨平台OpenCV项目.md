---
title: "使用CMake构建跨平台OpenCV项目"
categories: [ "opencv","c++" ]
tags: [  ]
draft: false
slug: "opencv/opencv-cmake-example"
date: "2020-09-22 13:35:00"
---

本文介绍了如何在windows上使用CMake构建跨平台的OpenCV项目，利用cmake解决每次使用opencv都需要重新配置vs属性的问题，可实现几行cmake代码一次构建，不同平台编译。

**准备**

开始之前的准备

- 安装好OpenCV,官方安装包或者自行编译都可以
- 安装好CMake,版本最好不要太老旧，推荐3.14+
- 安装好Visual Studio,越新越好，推荐2017+

**step1: 设置OpenCV_DIR环境变量**

新建一个名为`OpenCV_DIR`的系统环境变量，将opencv安装目录添加上，如下图
![OpenCV_DIR_ENV.png][1]

**step2: 创建工程目录以及代码文件**

创建main.cpp文件

```cpp
// cmake构建opencv示例
// @mango

#include "opencv2/opencv.hpp"

#include <iostream>

int main()
{
    std::cout<<"CMake构建opencv示例:"<<std::endl;
    std::cout<< cv::getBuildInformation() <<std::endl;
    return 0;
}
```

**step3: 编写CMakeLists.txt文件**

到这里你可能会担心不会CMake语法，编写CMakeLists.txt还需要重特地去学习一番，但其实OpenCV已经编写号一个示例了，项目配置OpenCV也非常简单，几句语句的功夫。在

```
..\sources\samples\cpp\example_cmake\
```

目录下可以找到,打开CMakeLists.txt

```cmake
# cmake needs this line
cmake_minimum_required(VERSION 2.8)

# Define project name
project(opencv_example_project)

# Find OpenCV, you may need to set OpenCV_DIR variable
# to the absolute path to the directory containing OpenCVConfig.cmake file
# via the command line or GUI
find_package(OpenCV REQUIRED)

# If the package has been found, several variables will
# be set, you can find the full list with descriptions
# in the OpenCVConfig.cmake file.
# Print some message showing some of them
message(STATUS "OpenCV library status:")
message(STATUS "    version: ${OpenCV_VERSION}")
message(STATUS "    libraries: ${OpenCV_LIBS}")
message(STATUS "    include path: ${OpenCV_INCLUDE_DIRS}")

if(CMAKE_VERSION VERSION_LESS "2.8.11")
  # Add OpenCV headers location to your include paths
  include_directories(${OpenCV_INCLUDE_DIRS})
endif()

# Declare the executable target built from your sources
add_executable(opencv_example example.cpp)

# Link your application with OpenCV libraries
target_link_libraries(opencv_example PRIVATE ${OpenCV_LIBS})
```

咱来一句一句简单看一下

```cmake
#规定cmake最低版本
cmake_minimum_required(VERSION 2.8) 

#项目名称
project(opencv_example_project)
```

```cmake
# 搜索opencv相关文件
find_package(OpenCV REQUIRED)
```

```cmake
# 引入opencv包含目录，这一步与在vs属性配置中配置包含目录相对应
include_directories(${OpenCV_INCLUDE_DIRS})
```

```cmake
# 添加可执行文件
add_executable(opencv_example example.cpp)
# 链接opencv库文件
target_link_libraries(opencv_example PRIVATE ${OpenCV_LIBS})
```

以上两步骤对应的是在vs配置opencv时库目录与附加依赖项的过程。那么参考opencv提供的示例，我们这次的demo也就非常容易了，稍加修改：

```cmake
cmake_minimum_required(VERSION 2.8)

project(opencv_example_project)

# 这一步要求设置号OpenCV_DIR环境变量，否则是无法正常找到的
find_package(OpenCV REQUIRED)

include_directories(${OpenCV_INCLUDE_DIRS})

add_executable(opencv_example main.cpp)

target_link_libraries(opencv_example PRIVATE ${OpenCV_LIBS})
```

**step4: 构建VS工程**

到这一步，就是构建VS项目工程了，与平时自己在windows编译opencv一样。有两种方式，一种是cmake gui的界面操作方式，就是网上大多数在windows平台编译opencv教程那样子；另一种方式是命令行操作，在linux平台编译项目就常用命令行方式，但其实在windows平台也适用。 


gui方式构建，如下图：

![GUI_Build.png][2]

命令行构建

```bash
cd build/
cmake -G "Visual Studio 15 2017" .. #win32
# cmake -G "Visual Studio 15 2017 Win64" .. #x64
```
看到如下两个done的消息提示就说明vs工程已经成功构建了

```
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found OpenCV: D:/opencv341/build (found version "3.4.10")
-- Configuring done
-- Generating done
```

到build目录下打开vs工程就可以进行编译执行了。

```
General configuration for OpenCV 3.4.10 =====================================
  Version control:               unknown

  Platform:
    Timestamp:                   2020-08-22T07:57:10Z
    Host:                        Windows 10.0.18363 AMD64
    CMake:                       3.18.1
    CMake generator:             Visual Studio 15 2017
    CMake build tool:            C:/Program Files (x86)/Microsoft Visual Studio/2017/Community/MSBuild/15.0/Bin/MSBuild.exe
    MSVC:                        1916

  CPU/HW features:
    Baseline:                    SSE SSE2
      requested:                 SSE2
    Dispatched code generation:  SSE4_1 SSE4_2 FP16 AVX
      requested:                 SSE4_1 SSE4_2 AVX FP16
      SSE4_1 (16 files):         + SSE3 SSSE3 SSE4_1
      SSE4_2 (2 files):          + SSE3 SSSE3 SSE4_1 POPCNT SSE4_2
      FP16 (1 files):            + SSE3 SSSE3 SSE4_1 POPCNT SSE4_2 FP16 AVX
      AVX (6 files):             + SSE3 SSSE3 SSE4_1 POPCNT SSE4_2 AVX
...
```

**跨平台：**

该工程无需修改在linux平台上也是可以直接编译的，相应命令行更为简单

```bash
cd build/
cmake ..
make 
```

---------------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/opencv/opencv-cmake-example.html

  [1]: https://mangoroom.cn/usr/uploads/2020/09/2557444240.png
  [2]: https://mangoroom.cn/usr/uploads/2020/09/3666645826.png