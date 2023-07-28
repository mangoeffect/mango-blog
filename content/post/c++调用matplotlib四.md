---
title: "c++调用matplotlib四"
categories: [ "工具" ]
tags: [ "c++","cmake","matplotlib","vscode","ubuntu" ]
draft: false
slug: "call-matplotlib-on-cpp-4"
date: "2022-09-28 21:34:00"
---

应网友的需求，介绍一下matplotlib-cpp在ubuntu平台vscode的使用方法，其实过程相比windows更容易一点，主要区别是CMakeLists.txt写法稍有不一样。这里简单记录一下步骤过程

## 准备vscode开发环境

参考[vscode+cmake跨平台c++开发方案](https://mangoroom.cn/tools/cpp-cross-platform-develop-sulotion-based-vscode-and-cmake.html)中准备好vscode的开发环境，这套配置在三个桌面端系统开发体验都一样，强烈推荐。文中介绍的是windows平台的配置，其他平台也大同小异，这里说下主要步骤

- 编译工具
```bash
# cmake
sudo apt install cmake
# 编译器（如果有需要）
sudo apt install gcc
```

- 安装 vscode

[https://code.visualstudio.com/](https://code.visualstudio.com/)，下载安装即可。

- 安装vscode扩展包

vscode扩展商店搜索`C/C++ Extension Pack`扩展安装，会自动安装必要的扩展。


## 安装python-matplotlib

ubuntu一般自带python， 如果没有自行安装也可以

```bash
sudo apt install python
pip3 install matplotlib
pip3 install numpy
```

## CMake配置工程


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
project (matplotlib_cpp)

# 设置c++编译器语言标准版本11以上
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 搜索python
find_package(Python3 COMPONENTS Interpreter Development REQUIRED)
# 是否需要numpy
find_package(Python3 COMPONENTS NumPy)

# 添加头文件定义
# add_definitions(-DMATPLOTLIBCPP_PYTHON_HEADER=Python.h)

# 可执行文件
add_executable(${PROJECT_NAME} main.cpp)

target_link_libraries(matplotlib_cpp INTERFACE
  Python3::Python
  Python3::Module
)
if(Python3_NumPy_FOUND)
  target_link_libraries(matplotlib_cpp INTERFACE
    Python3::NumPy
  )
else()
  target_compile_definitions(matplotlib_cpp INTERFACE WITHOUT_NUMPY)
endif()
```

最后，按照[vscode+cmake跨平台c++开发方案](https://mangoroom.cn/tools/cpp-cross-platform-develop-sulotion-based-vscode-and-cmake.html)中，点击cmake按钮，构建和边缘即可。

---------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/tools/call-matplotlib-on-cpp-4.html



