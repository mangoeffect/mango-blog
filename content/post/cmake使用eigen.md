---
title: "cmake使用eigen"
categories: [ "Eigen" ]
tags: [ "cmake","eigen","eigen-3.4.0" ]
draft: false
slug: "use-eigen-in-cmake"
date: "2021-09-23 08:31:00"
---

- cpp文件
编写以下main.cpp测试文件，内容是输出eigen的版本号

```cpp 
/**
 * @file main.cpp
 * @author mango (2321544362@qq.com)
 * @brief 查看eigen版本号
 * @version 0.1
 * @date 2021-09-22
 * 
 * @copyright Copyright (c) 2021
 * 
 */

#include <iostream>
#include "Eigen/Core"

int main(int argc, char** argv)
{
    std::cout<< "Eigen version is:\n";
    std::cout<< EIGEN_WORLD_VERSION << "." << EIGEN_MAJOR_VERSION << "." << EIGEN_MINOR_VERSION << std::endl;
    return 0;
}
```

- CMakeLists

在main.cpp同级目录编写以下CMakeLists.txt文件，为main.cpp导入eigen依赖

```cmake 
# cmake最低版本号要求
cmake_minimum_required(VERSION 3.14)

# 项目名称
project(eigen_demo)

# 设置Eigen3_DIR所在目录，对应eigen安装目录下的cmake目录，目录内包含有Eigen3Config.cmake等文件
set(Eigen3_DIR "./install/share/eigen3/cmake")

# 搜索查询Eigen3
find_package(Eigen3 REQUIRED NO_MODULE)

# 添加以main.cpp文件为基础的可执行目标文件
add_executable(${PROJECT_NAME} main.cpp)

# 为项目可行执行文件引入eigen依赖
target_link_libraries(${PROJECT_NAME} Eigen3::Eigen)
```

编译完后，执行cmake进行构建

```bash
mkdir build
cd build 

# linux
cmake  ../
make
./eigen_demo

# windows
cmake -G "Visual Studio 16 2019" ..
```

如果编译成功，执行将会输出以下结果

```bash
Eigen version is:
3.4.0
```
-------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/eigen/use-eigen-in-cmake.html



