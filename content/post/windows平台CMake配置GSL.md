---
title: "windows平台CMake配置GSL"
categories: [ "c++","工具" ]
tags: [ "c++","cmake","GSL" ]
draft: false
slug: "cmake-configure-gsl-on-windows"
date: "2020-09-23 18:49:00"
---

上一篇笔记[windows平台CMake编译GSL](https://mangoroom.cn/tools/use-cmake-build-gsl-on-windows.html)介绍了如何在windows平台使用CMake构建GSL库后，使用Visual Studio进行编译的过程，该篇笔记里编译完成后引用GSl采用的时Visual Studio的属性表配置，本篇笔记介绍在windows平台下如何使用CMake配置GSL库。


编译完成GSL后会产生一下三个目录

```bash
bin
include
lib
```
bin目录里面为一个配置GSL库的bash脚本，在windows平台下用不到我们暂时不用理会。我们将包含头文件的include以及包含库文件的lib拷贝至你的项目根目录gsl，接着新建一个cmake目录，假设项目暂且只有一个main.cpp文件，此时的项目目录应为：

```bash
cmake/
gls/include
gls/lib
main.cpp
```
接着我们在cmake目录之下新建一个FindGSL.cmake文件

```bash
cmake/FindGSL.cmake
gls/include
gls/lib
main.cpp
```
FindGSL.cmake文件用于自动搜索查找GSL库的相关文件，里面内容如下

```cmake
cmake_minimum_required(VERSION 3.12)

if(NOT GSL_FOUND)
    # 包含头文件目录
    set(GSL_INCLUDE_DIRS "${CMAKE_CURRENT_SOURCE_DIR}/gsl/include")
    # 库目录
    set(GSL_LIBRARIES_DIR "${CMAKE_CURRENT_SOURCE_DIR}/gsl/lib")
    # 查找gsl.lib gslcblas.lib库文件
    find_library(GSL_GSL_LIB NAMES gsl  PATHS ${GSL_LIBRARIES_DIR} PATH_SUFFIXES lib)
    find_library(GSL_GSLCBLAS_LIB NAMES gslcblas PATHS ${GSL_LIBRARIES_DIR} PATH_SUFFIXES lib) 
    set(GSL_LIBRARIES ${GSL_GSL_LIB} ${GSL_GSLCBLAS_LIB} )
    include(FindPackageHandleStandardArgs)
    find_package_handle_standard_args(GSL DEFAULT_MSG GSL_INCLUDE_DIRS GSL_LIBRARIES_DIR GSL_LIBRARIES)
endif()
```

接着创建CMakeLists.txt来构建项目

```bash
cmake/FindGSL.cmake
CMakeLists.txt
gls/include
gls/lib
main.cpp
```

CMakeLists.txt内容如下：

```cmake
cmake_minimum_required(VERSION 3.12)

project(gls_cmake_demo)

# Finde GSl
set(CMAKE_MODULE_PATH 
   ${CMAKE_CURRENT_SOURCE_DIR}/cmake
)

find_package(GSL REQUIRED)

if (GSL_FOUND)
    message(STATUS "GSL found successfully...")
    include_directories(${GSL_INCLUDE_DIRS})
else()
    message(FATAL_ERROR "Failed to find GLEW!!!")
endif()

add_executable(${PROJECT_NAME} main.cpp)

target_link_libraries(${PROJECT_NAME} ${GSL_LIBRARIES})
```

最后就可以使用CMakeLists.txt构建vs工程了，使用CMake可以采用GUI方式与命令行方式，GUI方式上一篇编译GSL的时候已经介绍过了，这次采用命令行方式编译一下

```bash
# 新建build目录来存放vs工程
mkdir build
cd build/

# 32位gls库就选择生成32位的vs项目工程(vs2017为例)
cmake -G "Visual Studio 15 2017" ..

# 64位
cmake -G "Visual Studio 15 2017 Win64" ..
```
看到如下提示信息就说明项目工程构建成功了，到build目录下打开就可以编译

```
...

-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found GSL: F:/Learning/GSL/gsl_cmake_demo/gsl/include
-- GSL found successfully...
-- Configuring done
-- Generating done
-- Build files have been written to: F:/Learning/GSL/gsl_cmake_demo/build
```

这次测试gsl用例还是与上篇一样

```cpp
//main.cpp
#include <stdio.h>
#include <gsl/gsl_sf_bessel.h>
int main()
{
	double x = 5.0;
	double y = gsl_sf_bessel_J0(x);
	printf("J0(%g) = %.18e\n", x, y);
	return 0;
}
```

编译运行后控制台输出

```bash
J0(5) = -1.775967713143382642e-01
```

-------------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/cpp/cmake-configure-gsl-on-windows.html

