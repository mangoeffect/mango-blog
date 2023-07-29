---
title: "windows平台clion配置opencv"
categories: [ "opencv","计算机视觉" ]
tags: [ "opencv","cmake","clion" ]
draft: false
slug: "opencv/clion-opencv-config-on-windows"
date: "2020-03-21 15:42:00"
---

本文简单记录了windows平台上clion配置OpenCV的过程，该配置适用于msvc或mingW编译的OpenCV。
![clion.png][1]

## 准备工作

在开始之前，你需要确认你已经做好以下的工作

- 已安装clion（版本越新越好，并已安装编译和调试器）
- 已经编译opencv（msvc或MingW，clion目前暂不支持msvc调试）

## 步骤

（1）新建工程
![project-folder.png][2]
（2）CMake文件

```python
cmake_minimum_required(VERSION 3.15)
project(opencv_config)

set(CMAKE_CXX_STANDARD 14)

# 设置OpenCV目录所在
set(OpenCV_DIR "D:\\opencv_mingw_build")

# 搜索OpenCV目录
find_package(OpenCV REQUIRED)

# 添加OpenCV头文件目录
include_directories(${OpenCV_INCLUDE_DIRS})

add_executable(opencv_config main.cpp)

# 链接OpenCV库文件
target_link_libraries(opencv_config ${OpenCV_LIBS})
```

（3）测试程序

```c++
#include <iostream>
#include "opencv2/opencv.hpp"

int main()
{
    std::cout << "OpenCV config sample." << std::endl;
    std::cout<< cv::getBuildInformation() <<std::endl;
    return 0;
}
```

（4）设置工作目录
![edit-configuration.png][3]
![working-directory-setting.png][4]
（5）运行输出：

```bash
OpenCV config sample.

General configuration for OpenCV 4.0.1 =====================================
  Version control:               unknown

  Platform:
    Timestamp:                   2019-03-01T05:09:02Z
    Host:                        Windows 10.0.17763 AMD64
    CMake:                       3.14.0-rc2
    CMake generator:             MinGW Makefiles
    CMake build tool:            C:/Qt/Qt5.12.0/Tools/mingw730_64/bin/mingw32-make.exe
    Configuration:               Release

  CPU/HW features:
    Baseline:                    SSE SSE2 SSE3
      requested:                 SSE3
    Dispatched code generation:  SSE4_1 SSE4_2 FP16 AVX AVX2
      requested:                 SSE4_1 SSE4_2 AVX FP16 AVX2 AVX512_SKX
      SSE4_1 (7 files):          + SSSE3 SSE4_1
      SSE4_2 (2 files):          + SSSE3 SSE4_1 POPCNT SSE4_2
      FP16 (1 files):            + SSSE3 SSE4_1 POPCNT SSE4_2 FP16 AVX
      AVX (5 files):             + SSSE3 SSE4_1 POPCNT SSE4_2 AVX
      AVX2 (13 files):           + SSSE3 SSE4_1 POPCNT SSE4_2 FP16 FMA3 AVX AVX2
....
```

至此，windows平台clion已经顺利配置OpenCV，希望对你有所帮助。

-----

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/opencv/clion-opencv-config-on-windows.html

  [1]: https://mangoroom.cn/usr/uploads/2020/03/2665346324.png
  [2]: https://mangoroom.cn/usr/uploads/2020/03/2883543192.png
  [3]: https://mangoroom.cn/usr/uploads/2020/03/3615255779.png
  [4]: https://mangoroom.cn/usr/uploads/2020/03/3164032241.png