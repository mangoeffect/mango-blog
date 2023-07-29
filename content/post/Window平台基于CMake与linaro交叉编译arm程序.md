---
title: "Window平台基于CMake与linaro交叉编译arm程序"
categories: [ "工具" ]
tags: [ "cmake","linaro","cross-compiling","windows","mingw" ]
draft: false
slug: "tools/how-to-use-linaro-and-cmake-compile-arm-program-on-windows"
date: "2021-03-14 20:42:00"
---

## 简介

本文介绍了在windows系统平台使用linaro交叉编译工具链，基于cmake和MinGW的make构建方式编译arm平台目标程序。
    
## 环境准备

交叉编译之前需要准备好以下环境和文件，cmake与MinGW安装网上搜索下载安装即可。linaro编译工具链根据自己机器的目标平台到linaro官网选择对应的版本下载即可，本文基于`gcc-linaro-7.5.0-2019`版本实验，也适用于其他版本。

- cmake
- MinGW
- linaro

## 步骤

### 编写工具链toolchain.cmake文件

在工具链根目录新建cmake目录，里面存放该工具链的toolchain.cmake文件。toolchain.cmake文件内容如下。

```cmake
# 设置目标系统、处理器架构
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR arm)

# 设置工具链目录
set(TOOL_CHAIN_DIR ${CMAKE_CURRENT_LIST_DIR}/../)
set(TOOL_CHAIN_INCLUDE ${TOOL_CHAIN_DIR}/arm-linux-gnueabihf/include ${TOOL_CHAIN_DIR}/arm-linux-gnueabihf/libc/usr/include)
set(TOOL_CHAIN_LIB  ${TOOL_CHAIN_DIR}/arm-linux-gnueabihf/lib ${TOOL_CHAIN_DIR}/arm-linux-gnueabihf/libc/usr/lib)

# 设置编译器位置
set(CMAKE_C_COMPILER "${TOOL_CHAIN_DIR}/bin/arm-linux-gnueabihf-gcc.exe")
set(CMAKE_CXX_COMPILER "${TOOL_CHAIN_DIR}/bin/arm-linux-gnueabihf-g++.exe")

# 设置cmake查找主路径
set(CMAKE_FIND_ROOT_PATH ${TOOL_CHAIN_DIR}/arm-linux-gnueabihf)

set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
# 只在指定目录下查找库文件
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
# 只在指定目录下查找头文件
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
# 只在指定目录下查找依赖包
set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)

# 包含工具链文件
include_directories(
    ${TOOL_CHAIN_DIR}/arm-linux-gnueabihf/include
    ${TOOL_CHAIN_DIR}/arm-linux-gnueabihf/libc/usr/include)

# 设置CMAKE_INCLUDE_PATH
set(CMAKE_INCLUDE_PATH ${TOOL_CHAIN_INCLUDE})

# 设置CMAKE_LIBRARY_PATH
set(CMAKE_LIBRARY_PATH ${TOOL_CHAIN_LIB})
```

toolchain.cmake文件内容主要描述了交叉编译工具链相关程序（如编译器、汇编器链接器）、包含头文件与库文件的存放路径，以便CMake构建Makefiles文件时正确找到并使用他。

### 测试

#### 测试程序

1. 可执行文件

```cpp
int main(int argc, char* argv[])
{
    return 0;
}
```

2. 动态库文件

```cpp
#ifndef ADD_H_
#define ADD_H_

int add(const int& a, const int& b);

#endif //!ADD_H_
```

```cpp
#include "add.h"

int add(const int& a, const int& b)
{
    return a + b;
}
```

#### 编写测试程序CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.14)

# 设置工具链cmake文件
set(CMAKE_TOOLCHAIN_FILE ${CMAKE_SOURCE_DIR}/gcc-linaro-7.5.0-2019.12-i686-mingw32_arm-linux-gnueabihf/cmake/toolchain.cmake)

project(cross_compile_demo)
# 设置make程序位置
set(CMAKE_MAKE_PROGRAM = "C:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/bin/mingw32-make.exe")

add_executable(${PROJECT_NAME} main.cpp)

add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/add)
```

```cmake
cmake_minimum_required(VERSION 3.14)

project(add)

add_library(${PROJECT_NAME} SHARED add.h add.cpp)
```

### 编译

构建编译可以选择基于cmake命令行或GUI的方式，以下两种方法任意二选一即可。

- 命令行构建编译

cd到项目根目录，新建build目录存放构建、编译文件。

```bash
cd youproject_dir
mkdir build
cd build
cmake -G "MinGW Makefiles" ..
mingw32-make.exe 
```

- GUI构建编译

1. 打开cmake GUI程序
2. 选择项目根目录，以及build目录

![select-directories.png][1]

3. 点击configurate,配置。选择`MinGW Makefiles`与`Specify toolchain files for cross-compling`

![generator.png][2]
4. 选择toolchain.camke文件

![toolchain-files.png][3]
5. 点击generate

![cmake-done.png][4]
6. 打开控制台cd到build目录执行make编译

```bash
mingw32-make.exe 
```
执行编译，结束后生成目标文件。

```bash
Scanning dependencies of target cross_compile_demo
[ 25%] Building CXX object CMakeFiles/cross_compile_demo.dir/main.cpp.o
[ 50%] Linking CXX executable cross_compile_demo
[ 50%] Built target cross_compile_demo
[100%] Built target add
```

```bash
add/                 CMakeCache.txt  cross_compile_demo
cmake_install.cmake  CMakeFiles/     Makefile
```

```bash
cmake_install.cmake  CMakeFiles/  libadd.so  Makefile
```

-----------------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/tools/how-to-use-linaro-and-cmake-compile-arm-program-on-windows.html

  [1]: https://mangoroom.cn/usr/uploads/2021/03/3735418504.png
  [2]: https://mangoroom.cn/usr/uploads/2021/03/2486819531.png
  [3]: https://mangoroom.cn/usr/uploads/2021/03/3196570889.png
  [4]: https://mangoroom.cn/usr/uploads/2021/03/344634029.png