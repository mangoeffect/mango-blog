---
title: "CMake与framework"
draft: false
date: 2023-08-16T20:57:03+08:00
categories: [ "工具" ]
tags: [ "cmake", "framework", "macos"]
slug: "tools/cmake-and-framework"
---


## Framework

framework 是苹果平台的一种程序封装框架，与 c++ 的动态库和静态库有所不同。在苹果开发框架下使用方便且通用，详情参考

[https://www.jianshu.com/p/ee2affaa3bac](https://www.jianshu.com/p/ee2affaa3bac)

## CMake 生成 Framework

cmake 支持将 c++ 项目封编译成 framework 格式依赖。

```cpp
/**
 * @file model.hpp
 * @author your name (you@domain.com)
 * @brief 
 * @version 0.1
 * @date 2023-08-16
 * 
 * @copyright Copyright (c) 2023
 * 
 */

#ifndef MODEL_HPP
#define MODEL_HPP

int add(int a, int b);

#endif //MODEL_HPP
```

```cpp
#include "model.hpp"

int add(int a, int b)
{
    return a + b;
}
```

参考 cmake 官网示例 [https://cmake.org/cmake/help/v3.13/prop_tgt/FRAMEWORK.html](https://cmake.org/cmake/help/v3.13/prop_tgt/FRAMEWORK.html)

```
cmake_minimum_required(VERSION 3.14)

project(model)

add_library(model SHARED
            model.cpp
            model.hpp
)
set_target_properties(model PROPERTIES
  FRAMEWORK TRUE
  FRAMEWORK_VERSION C
  MACOSX_FRAMEWORK_IDENTIFIER com.cmake.dynamicFramework
  MACOSX_FRAMEWORK_INFO_PLIST Info.plist
  # "current version" in semantic format in Mach-O binary file
  VERSION 16.4.0
  # "compatibility version" in semantic format in Mach-O binary file
  SOVERSION 1.0.0
  PUBLIC_HEADER model.hpp
  XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY "iPhone Developer")
```

编译生成 framework

```bash
mkdir build
cd build
cmake ..
make
```

成功生成 framework

```bash
[ 50%] Building CXX object CMakeFiles/model.dir/model.cpp.o
[100%] Linking CXX shared library model.framework/model
Copying OS X content model.framework/Versions/C/Headers/model.hpp
```

查看生成 framework 的文件结构

```bash
tree
.
├── Headers -> Versions/Current/Headers
├── Resources -> Versions/Current/Resources
├── Versions
│   ├── C
│   │   ├── Headers
│   │   │   └── model.hpp
│   │   └── model
│   └── Current -> C
└── model -> Versions/Current/model
```

## CMake 引用

```
cmake_minimum_required(VERSION 3.14)

project(import_framework)

# include framework headers
include_directories(./model.framework/Headers)

add_executable(${PROJECT_NAME} "main.cpp")

# link
target_link_libraries(${PROJECT_NAME}  "-framework model")

# framework search dir
set_target_properties(${PROJECT_NAME} PROPERTIES
   LINK_FLAGS "-Wl,-F  ${CMAKE_CURRENT_SOURCE_DIR}"
)
```

```cpp
/**
 * @file main.cpp
 * @author your name (you@domain.com)
 * @brief 
 * @version 0.1
 * @date 2023-08-16
 * 
 * @copyright Copyright (c) 2023
 * 
 */

#include "model.hpp"
#include <iostream>

int main(int argc, char** argv)
{
    std::cout << "1+1=" << add(1,1) << std::endl;
    return 0;
}
```
