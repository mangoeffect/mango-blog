---
title: "CMake学习笔记"
categories: [ "工具" ]
tags: [ "cmake" ]
draft: false
slug: "tools/cmake-learning"
date: "2020-03-04 10:52:00"
---

## CMake简介

![113px-Cmake.svg.png][1]

CMake是一个开源、跨平台的Make工具，英文全称为cross platform make.它使用简单的脚本语句来描述所有平台的编译过程。类似的工具还有Qt的qmake、微软的MS nmake、GNU的GNU make 等。不同的make工具遵循不同的标准，也适用于不同的操作系统平台。而CMake相比之下最突出的优点是跨平台，只需要编写一个CMakeList.txt指定编译流程，然后就可以根据用户平台的不同而生成当前平台的Makefile和工程文件。比如著名的opencv就是采用了CMake，在编译opencv源码的时候就可以知道，在window编译时首先会根据源码目录下的CMakeList.txt文件生成VS解决方案，而后再使用VS进行编译操作；在Linux平台编译时，也是根据CMakeList.txt文件先产生Makefile文件，再使用Linux的Make工具进行编译操作。

---

作为一个优秀的跨平台开源Make工具，使用CMake作为构建工具的项目有很多，比较著名的有：

- OpenCV
- Boost c++ libraries
- LLVM  
- clang

...
更多的开源库可在维基百科查看[CMake-维基百科](https://zh.wikipedia.org/wiki/CMake#%E4%BD%BF%E7%94%A8CMake%E7%9A%84%E6%87%89%E7%94%A8%E8%BB%9F%E9%AB%94)

## 简单文件

- cpp源文件

```c++
//main.cpp
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

- CMakeLists文件

```python
# 1 cmake version
cmake_minimum_required(VERSION 3.13)

# 2 project name
project(demo1)

# 3 cpp version
set(CMAKE_CXX_STANDARD 14)

# 4 executable file
add_executable(demo1 main.cpp)
```
### 笔记

- 构建文件名为CMakeLists.txt
- 最简单的单文件构CMakeLists.txt包含三个部分：1（cmake最低版本要求）、2（项目信息）、4（指定生成目标文件）
- CMakeLists.txt详解


```python
# 1 cmake version
cmake_minimum_required(VERSION 3.13)
```

cmake最低版本要求，还有以下的扩展写法

```python
cmake_minimum_required(VERSION 3.1...3.15)

# If CMake version is less than 3.12, the if block will be true, and the policy will be set to the current CMake version.
if(${CMAKE_VERSION} VERSION_LESS 3.12)
    cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
endif()
```

```python
# 2 project name
project(demo1)
```

项目信息，描述项目信息的名称

```python

# 3 cpp version
set(CMAKE_CXX_STANDARD 14)
```

设定使用c++编译器的版本，使用符合c++14标准的编译器编译项目。

```python
# 4 executable file
add_executable(demo1 main.cpp)
```
指定生成的目标信息，将main.cpp文件编译成一个名称为demo1的可执行文件。

## references

[【1】CMake入门实战](https://www.hahack.com/codes/cmake/#)
[【2】An Introduction to Modern CMake](https://cliutils.gitlab.io/modern-cmake/)
[【3】CMake Tutorial](https://cmake.org/cmake/help/v3.17/guide/tutorial/index.html)
[【4】CMake-维基百科](https://zh.wikipedia.org/wiki/CMake#%E4%BD%BF%E7%94%A8CMake%E7%9A%84%E6%87%89%E7%94%A8%E8%BB%9F%E9%AB%94)

---

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/tools/cmake-learning.html

 [1]: https://mangoroom.cn/usr/uploads/2020/03/1290917933.png