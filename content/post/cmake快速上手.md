---
title: "cmake快速上手"
categories: [ "工具" ]
tags: [ "opencv","cmake","Qt","pcl","openmp" ]
draft: false
slug: "tools/cmake-hands-on"
date: "2021-12-04 10:21:00"
---

## cmake简介

### cmake是什么

CMake是个一个开源的跨平台自动化建构系统，用来管理软件建置的程序，并不依赖于某特定编译器，并可支持多层目录、多个应用程序与多个库。 它用配置文件控制建构过程（build process）的方式和Unix的make相似，只是CMake的配置文件取名为CMakeLists.txt。CMake并不直接建构出最终的软件，而是产生标准的建构档（如Unix的Makefile或Windows Visual C++的projects/workspaces），然后再依一般的建构方式使用。这使得熟悉某个集成开发环境（IDE）的开发者可以用标准的方式建构他的软件，这种可以使用各平台的原生建构系统的能力是CMake和SCons等其他类似系统的区别之处。 CMake配置文件（CMakeLists.txt）可设置源代码或目标程序库的路径、产生适配器（wrapper）、还可以用任意的顺序建构可执行文件。CMake支持in-place建构（二进档和源代码在同一个目录树中）和out-of-place建构（二进档在别的目录里），因此可以很容易从同一个源代码目录树中建构出多个二进档。CMake也支持静态与动态程序库的建构。

“CMake”这个名字是"Cross platform Make"的缩写。虽然名字中含有"make"，但是CMake和Unix上常见的“make”系统是分开的，而且更为高端。 它可与原生建置环境结合使用，例如：make、ninja、苹果的Xcode与微软的Visual Studio。

### cmake优点

- 跨平台: windows、linux、macos均支持。
- 主流IDE普遍支持：windows可以构建vs工程，vs2019已原生支持，可以直接打开cmake构建项目。 qtcreator、clion、vscode(需搭配插件)。
- 协同开发：基于cmake跨平台、跨ide特性，可以实现一套代码不同系统、不同开发工具协同开发体验，实现协同开发之间只需关心编码部分无线关心系统平台、开发工具或第三方库的差异和限制。
- c++主流第三方库普遍提供cmake支持，引入第三方库方便快捷。Qt、OpencCV、PCL、boost等都提供cmake支持。

## 安装cmake

### windows

官网下载链接

[https://cmake.org/download/](https://cmake.org/download/)

下载安装包

![download_cmake_windows_install_pack](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-blog-imagesdownload_cmake_windows_install_pack.png)

设置环境变量,如果未设置环境变量则无法在命令行终端中使用cmake

![setting_cmake_path](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-blog-imagessetting_cmake_path.png)

### linux

```bash
sudo apt install cmake
```

## 如何使用cmake构建已有项目

### GUI构建VS工程

1. 选择项目包含CMakeLists.txt源码根目录与VS生成目录

2. Configure

3. Generate

### 命令行构建

```bash
# 进入项目包含CMakeLists.txt源码根目录
cd  project_root_dir 
# 创建build目录存放生成的VS工程
mkdir build
# 生成VS工程
cmake -G "Visual Studio 15 2017" .. # 默认32位
cmake -G "Visual Studio 16 2019" .. # 默认64位

cmake -G "Visual Studio 16 2019" Win32 .. 
# 或
cmake -G "Visual Studio 16 2019" -A Win32 .. # 指定生成vs2019 32位工程

cmake -G "Visual Studio 15 2017" Win64 ..    # 指定生成vs2017 64位工程
```

## cmake基本语法

- 构建文件命名CMakeLists.txt
- 支持大写、小写、混合大小写的命令
- 注释格式

```cmake
# 这是注释
```

- 变量赋值

```cmake
set(SOURCE_DIR "./src") # 设置SOURCE_DIR路径变量
```

- 变量取值

格式为：

```
${变量名}
```

```cmake
set(INCLUDE_DIR "./src") # 设置INCLUDE_DIR路径变量
include_directories(${INCLUDE_DIR})
```

- 条件分支

```cmake
if( ... )
    #分支1
elseif( ... )
    #分支2
endif()
```

- cmake_minimum_required

```cmake
cmake_minimum_required(VERSION 3.14)
```

如果CMake的当前版本低于指定的版本，它会停止处理工程文件，并报告错误。

- project

```cmake
project(project_name CXX)
```

为本工程设置一个工程名。而且，该命令还将变量`<projectName>`_BINARY_DIR和`<projectName>`_SOURCE_DIR设置为对应值。后面的可选项还可以让你指定你的工程可以支持的语言。比如CXX(即C++)，C，Fortran，等等。在默认条件下，支持C和CXX语言。例如，如果你没有C++编译器，你可以通过列出你想要支持的语言，例如C，来明确地禁止对它的检查。使用特殊语言"NONE"，针对任何语言的检查都会被禁止。

- add_executable(`<name>` main.cpp)

引入一个名为`<name>`的可执行目标，该目标会由调用该命令时在源文件列表中指定的源文件来构建。`<name>`对应于逻辑目标名字，并且在工程范围内必须是全局唯一的。被构建的可执行目标的实际文件名将根据具体的本地平台创建出来（比如`<name>`.exe或者仅仅是`<name>`）。

- target_link_libraries(`executable_name>` `<libs>`)

```cmake
# 链接OpenCV库
target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBS})
```

## 最基础项目的CMakeLists.txt

项目目录：

```bash
CMakeLists.txt
main.cpp
```

```cmake
# 最低cmake版本要求
cmake_minimum_required(VERSION 3.14)

# 项目名称
project(simple_project_demo)

# 编译生成可执行文件
add_executable(${PROJECT_NAME} main.cpp)
```

## 多文件

```cmake
# 最低cmake版本要求
cmake_minimum_required(VERSION 3.14)

# 项目名称
project(multiple_files_demo)

# 包含当前目录
include_directories(./)

# 编译生成可执行文件
add_executable(${PROJECT_NAME} main.cpp add.cpp)
```

## 多项目多目录

项目根目录CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.14)

# 项目名称
project(multi_project)

add_subdirectory(project1_dir)
add_subdirectory(project1_dir)
```

子项目1: CMakeLists.txt

```cmake
# 最低cmake版本要求
cmake_minimum_required(VERSION 3.14)

# 项目名称
project(subproject1)

# 包含当前目录
include_directories(./)

# 编译生成可执行文件
add_executable(${PROJECT_NAME} main.cpp)
```


子项目2: CMakeLists.txt

```cmake
# 最低cmake版本要求
cmake_minimum_required(VERSION 3.14)

# 项目名称
project(subproject2)

# 包含当前目录
include_directories(./)

# 编译生成可执行文件
add_executable(${PROJECT_NAME} main.cpp)
```

## 生成动/静态库


```cmake
cmake_minimum_required(VERSION 3.14)

project(add)

# 生成静态库
add_library(${PROJECT_NAME} STATIC add.h add.cpp)
# 生成动态库
# add_library(${PROJECT_NAME} SHARED add.h add.cpp)
```

## 引用第三方库

### OpenCV

```cmake
# 最低cmake版本要求
cmake_minimum_required(VERSION 3.14)

# 项目名称
project(import_opencv_demo)

# 查找OpenCV
find_package(OpenCV REQUIRED)

# 包含OpenCV头文件目录
include_directories(${OpenCV_INCLUDE_DIRS})

# 编译生成可执行文件
add_executable(${PROJECT_NAME} main.cpp)

# 链接OpenCV库文件
target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBS})
```

### OpenMP

```cmake
# 最低cmake版本要求
cmake_minimum_required(VERSION 3.14)

# 项目名称
project(import_openmp)

# 搜索OpenMP
find_package(OpenMP)

# 编译main.cpp文件生成可执行文件
add_executable(${PROJECT_NAME} "main.cpp")

# 链接OpenMP
if(OpenMP_CXX_FOUND)
    target_link_libraries(${PROJECT_NAME} PUBLIC OpenMP::OpenMP_CXX)
endif()
```

### Qt

文件目录如下：

```bash
│  CMakeLists.txt
│  main.cpp
│  mainwindow.cpp
│  mainwindow.h
│  mainwindow.ui
```

CMakeLists.txt脚本文件

```cmake
# cmake最低版本号要求
cmake_minimum_required(VERSION 3.14)

# 项目名称
project(import_qt_demo)

# qt相关设置，设置moc、rcc、uic
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON) #打开全局rcc，本示例中没有使用qrc，此句可以去掉
set(CMAKE_AUTOUIC ON) 

# 包含当前目录
include_directories(./)
# 搜索Qt5
find_package(Qt5 COMPONENTS Widgets REQUIRED)

# 编译生成可执行文件
add_executable(${PROJECT_NAME}
main.cpp 
mainwindow.cpp
mainwindow.ui
)

# 链接qt库文件
target_link_libraries(${PROJECT_NAME}  Qt5::Widgets)
```

### PCL

```cmake
# 最低cmake版本要求
cmake_minimum_required(VERSION 3.12)

# 项目名称
project(import_pcl)

# 搜索PCL
find_package(PCL REQUIRED) 
include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

# 编译main.cpp文件生成可执行程序
add_executable(${PROJECT_NAME} main.cpp)

# 链接PCL库
target_link_libraries(${PROJECT_NAME} ${PCL_LIBRARIES})
```


### 任意

有些第三方库并未基于CMake构建而得，提供SDK的时候并未提供PackageConfig.cmake文件，往往只提供了库文件和包含头文件。此时可以通过编写FindXXX.cmake文件实现对第三方库的引入。SDK目录一般如下结构

```bash
---root:
      include:  # 存放头文件
      lib:      # lib文件
        debug:
        release:      
      bin:      # dll文件
```

此时在SDK根目录新建一个cmake目录存放FindXXX.cmake文件：

```bash
---root:
      cmake:
      include:  # 存放头文件
      lib:      # lib文件
        debug:
        release:      
      bin:      # dll文件
```

以OpenCV为例，假如所得OpenCV安装包只有头文件和库文件，通过以下方法可以引入OpenCV。

```bash
|-cmake
|      FindOpenCV.cmake
├─bin
│      opencv_ffmpeg3410_64.dll
│
├─include
    ...#头文件
└─lib
    ├─debug
    │      opencv_world3410d.lib
    │
    └─release
            opencv_world3410.lib
```

其中FindOpenCV.cmake内容为：

```cmake
#--------------------使用示例--------------------
# 
# set(CMAKE_MODULE_PATH = ${CMAKE_CURRENT_SOURCE_DIR}/cmake) 
# project(import_OpenCV_demo) 
# find_package(OpenCV REQUIRED) 
# include_directories(${OpenCV_INCLUDE_DIRS}) 
# add_executable(${PROJECT_NAME} main.cpp) 
# target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBRARIES}) 
# 
# ---------------------------------------------
# 
# 最低版本号要求
cmake_minimum_required(VERSION 3.12) 
# 
if(NOT OpenCV_FOUND) 
   # 设置头文件目录、库目录路径（相对路径）
   set(OpenCV_INCLUDE_DIRS "${CMAKE_CURRENT_LIST_DIR}/../include")
   set(OpenCV_LIBRARIE_DIR_DEBUG "${CMAKE_CURRENT_LIST_DIR}/../lib/debug" ) #debug 
   set(OpenCV_LIBRARIE_DIR_RELEASE "${CMAKE_CURRENT_LIST_DIR}/../lib/release" ) #release 
   set(OpenCV_LIBRARIES_DIR ${OpenCV_LIBRARIE_DIR_DEBUG} ${OpenCV_LIBRARIE_DIR_RELEASE}) 
# 
   # 搜索debug库文件
   find_library(opencv_world3410d_LIBRARY_DEBUG NAMES opencv_world3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
# 
   # 搜索release库文件
   find_library(opencv_world3410_LIBRARY_DEBUG NAMES opencv_world3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
# 
   # 合并debug、release库文件只一个变量（cmake会自动区分配置debug和release)
   include(SelectLibraryConfigurations) 
   select_library_configurations( opencv_world3410d ) 
   set(OpenCV_LIBRARIES  ${opencv_world3410d_LIBRARY} ) 
# 
   # 将搜索所得变量签名，并标记为高级变量，以供CMakeLists.txt引入使用
   include(FindPackageHandleStandardArgs) 
   find_package_handle_standard_args(OpenCV DEFAULT_MSG OpenCV_INCLUDE_DIRS OpenCV_LIBRARIES_DIR OpenCV_LIBRARIES) 
   mark_as_advanced(OpenCV_INCLUDE_DIRS OpenCV_LIBRARIES_DIR  OpenCV_LIBRARIES) 
endif() 
```

编写FindXXX.cmake这种方式适用于任意的第三方库引入，但缺点是每次都需重新编写。一旦库文件比较多写起来就非常麻烦。为此斜率一个脚本自动生成。只需要将改脚本放置第三方库根目录运行即可：

```bash
├─bin
│     .. # dll
│
├─include
    ...#头文件
└─lib
    ├─debug
    │    ... # lib文件debug
    │
    └─release
         ... # lib文件release
|- auto_generate_findxxx_cmake.py
```

运行脚本即可自动生成，以下为自动生成的FindOpenCV.cmake。

```cmake
#--------------------demo--------------------
# 
# set(CMAKE_MODULE_PATH = ${CMAKE_CURRENT_SOURCE_DIR}/cmake) 
# project(import_OpenCV_demo) 
# find_package(OpenCV REQUIRED) 
# include_directories(${OpenCV_INCLUDE_DIRS}) 
# add_executable(${PROJECT_NAME} main.cpp) 
# target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBRARIES}) 
# 
# ---------------------------------------------
# 
cmake_minimum_required(VERSION 3.12) 
# 
if(NOT OpenCV_FOUND) 
   set(OpenCV_INCLUDE_DIRS "${CMAKE_CURRENT_LIST_DIR}/../include")
   set(OpenCV_LIBRARIE_DIR_DEBUG "${CMAKE_CURRENT_LIST_DIR}/../lib/debug" ) #debug 
   set(OpenCV_LIBRARIE_DIR_RELEASE "${CMAKE_CURRENT_LIST_DIR}/../lib/release" ) #release 
   set(OpenCV_LIBRARIES_DIR ${OpenCV_LIBRARIE_DIR_DEBUG} ${OpenCV_LIBRARIE_DIR_RELEASE}) 
# 
   #find and set the debug libraries. 
   find_library(opencv_calib3d3410d_LIBRARY_DEBUG NAMES opencv_calib3d3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_core3410d_LIBRARY_DEBUG NAMES opencv_core3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_dnn3410d_LIBRARY_DEBUG NAMES opencv_dnn3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_features2d3410d_LIBRARY_DEBUG NAMES opencv_features2d3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_flann3410d_LIBRARY_DEBUG NAMES opencv_flann3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_highgui3410d_LIBRARY_DEBUG NAMES opencv_highgui3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_imgcodecs3410d_LIBRARY_DEBUG NAMES opencv_imgcodecs3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_imgproc3410d_LIBRARY_DEBUG NAMES opencv_imgproc3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_ml3410d_LIBRARY_DEBUG NAMES opencv_ml3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_objdetect3410d_LIBRARY_DEBUG NAMES opencv_objdetect3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_photo3410d_LIBRARY_DEBUG NAMES opencv_photo3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_shape3410d_LIBRARY_DEBUG NAMES opencv_shape3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_stitching3410d_LIBRARY_DEBUG NAMES opencv_stitching3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_superres3410d_LIBRARY_DEBUG NAMES opencv_superres3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_video3410d_LIBRARY_DEBUG NAMES opencv_video3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_videoio3410d_LIBRARY_DEBUG NAMES opencv_videoio3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
   find_library(opencv_videostab3410d_LIBRARY_DEBUG NAMES opencv_videostab3410d  PATHS ${OpenCV_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) 
# 
   #find and set the release libraries. 
   find_library(opencv_calib3d3410_LIBRARY_DEBUG NAMES opencv_calib3d3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_core3410_LIBRARY_DEBUG NAMES opencv_core3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_dnn3410_LIBRARY_DEBUG NAMES opencv_dnn3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_features2d3410_LIBRARY_DEBUG NAMES opencv_features2d3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_flann3410_LIBRARY_DEBUG NAMES opencv_flann3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_highgui3410_LIBRARY_DEBUG NAMES opencv_highgui3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_imgcodecs3410_LIBRARY_DEBUG NAMES opencv_imgcodecs3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_imgproc3410_LIBRARY_DEBUG NAMES opencv_imgproc3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_ml3410_LIBRARY_DEBUG NAMES opencv_ml3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_objdetect3410_LIBRARY_DEBUG NAMES opencv_objdetect3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_photo3410_LIBRARY_DEBUG NAMES opencv_photo3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_shape3410_LIBRARY_DEBUG NAMES opencv_shape3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_stitching3410_LIBRARY_DEBUG NAMES opencv_stitching3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_superres3410_LIBRARY_DEBUG NAMES opencv_superres3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_video3410_LIBRARY_DEBUG NAMES opencv_video3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_videoio3410_LIBRARY_DEBUG NAMES opencv_videoio3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
   find_library(opencv_videostab3410_LIBRARY_DEBUG NAMES opencv_videostab3410  PATHS ${OpenCV_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) 
# 
   # merge debug and release libs to OpenCV_LIBRARIES 
   include(SelectLibraryConfigurations) 
   select_library_configurations( opencv_calib3d3410d ) 
   select_library_configurations( opencv_core3410d ) 
   select_library_configurations( opencv_dnn3410d ) 
   select_library_configurations( opencv_features2d3410d ) 
   select_library_configurations( opencv_flann3410d ) 
   select_library_configurations( opencv_highgui3410d ) 
   select_library_configurations( opencv_imgcodecs3410d ) 
   select_library_configurations( opencv_imgproc3410d ) 
   select_library_configurations( opencv_ml3410d ) 
   select_library_configurations( opencv_objdetect3410d ) 
   select_library_configurations( opencv_photo3410d ) 
   select_library_configurations( opencv_shape3410d ) 
   select_library_configurations( opencv_stitching3410d ) 
   select_library_configurations( opencv_superres3410d ) 
   select_library_configurations( opencv_video3410d ) 
   select_library_configurations( opencv_videoio3410d ) 
   select_library_configurations( opencv_videostab3410d ) 
   set(OpenCV_LIBRARIES  ${opencv_calib3d3410d_LIBRARY}  ${opencv_core3410d_LIBRARY}  ${opencv_dnn3410d_LIBRARY}  ${opencv_features2d3410d_LIBRARY}  ${opencv_flann3410d_LIBRARY}  ${opencv_highgui3410d_LIBRARY}  ${opencv_imgcodecs3410d_LIBRARY}  ${opencv_imgproc3410d_LIBRARY}  ${opencv_ml3410d_LIBRARY}  ${opencv_objdetect3410d_LIBRARY}  ${opencv_photo3410d_LIBRARY}  ${opencv_shape3410d_LIBRARY}  ${opencv_stitching3410d_LIBRARY}  ${opencv_superres3410d_LIBRARY}  ${opencv_video3410d_LIBRARY}  ${opencv_videoio3410d_LIBRARY}  ${opencv_videostab3410d_LIBRARY} ) 
# 
   include(FindPackageHandleStandardArgs) 
   find_package_handle_standard_args(OpenCV DEFAULT_MSG OpenCV_INCLUDE_DIRS OpenCV_LIBRARIES_DIR OpenCV_LIBRARIES) 
   mark_as_advanced(OpenCV_INCLUDE_DIRS OpenCV_LIBRARIES_DIR  OpenCV_LIBRARIES) 
endif() 
```

自动生成脚本，使用之前一般只需要更改变量lib_name为库名称即可

```python
# 自动生成FindXXX.cmake脚本
import sys, getopt
import os


def write_help_info(file, lib_name):
    file.writelines("#--------------------demo--------------------\n")
    file.writelines("# \n")
    file.writelines("# set(CMAKE_MODULE_PATH = ${CMAKE_CURRENT_SOURCE_DIR}/cmake) \n")
    file.writelines("# project(import_" + lib_name + "_demo) \n")
    file.writelines("# find_package(" + lib_name + " REQUIRED) \n")
    file.writelines("# include_directories(${" + lib_name + "_INCLUDE_DIRS}) \n")
    file.writelines("# add_executable(${PROJECT_NAME} main.cpp) \n")
    file.writelines("# target_link_libraries(${PROJECT_NAME} ${" + lib_name + "_LIBRARIES}) \n")
    file.writelines("# \n")
    file.writelines("# ---------------------------------------------\n")

def search_lib_files(file_dir):
    libs = []
    for root, dirs, files in os.walk(file_dir):
        for file in files:
            if os.path.splitext(file)[1] == '.lib':
                libs.append(os.path.splitext(file)[0])
    return libs

def generate_cmake_file(argv):
    lib_name = "OpenCV"
    mini_cmake_version = "3.12"
    include_dir_name = "include"
    debug_lib_dir = "lib/debug"
    release_lib_dir = "lib/release"
    file = open("Find" + lib_name + ".cmake","a")

    write_help_info(file, lib_name)
    file.writelines("# \n")
    file.writelines("cmake_minimum_required(VERSION " + mini_cmake_version + ") \n")
    file.writelines("# \n")
    file.writelines("if(NOT " + lib_name + "_FOUND) \n")
    file.writelines("   set(" + lib_name +"_INCLUDE_DIRS \"${CMAKE_CURRENT_LIST_DIR}/../" + include_dir_name + "\")\n")
    file.writelines("   set(" + lib_name +"_LIBRARIE_DIR_DEBUG \"${CMAKE_CURRENT_LIST_DIR}/../" + debug_lib_dir + "\" ) #debug \n")
    file.writelines("   set(" + lib_name +"_LIBRARIE_DIR_RELEASE \"${CMAKE_CURRENT_LIST_DIR}/../" + release_lib_dir + "\" ) #release \n")
    file.writelines("   set(" + lib_name +"_LIBRARIES_DIR ${" + lib_name + "_LIBRARIE_DIR_DEBUG} ${" + lib_name + "_LIBRARIE_DIR_RELEASE}) \n")

    current_dir = os.getcwd()
    debug_libs = search_lib_files(current_dir + "/" + debug_lib_dir)
    release_libs = search_lib_files(current_dir + "/" + release_lib_dir)
    file.writelines("# \n")
    file.writelines("   #find and set the debug libraries. \n")
    for d_lib in debug_libs:
        file.writelines("   find_library(" + d_lib + "_LIBRARY_DEBUG NAMES " + d_lib + "  PATHS ${" + lib_name + "_LIBRARIE_DIR_DEBUG} PATH_SUFFIXES lib) \n")
    file.writelines("# \n")
    file.writelines("   #find and set the release libraries. \n")
    for r_lib in release_libs:
        file.writelines("   find_library(" + r_lib + "_LIBRARY_DEBUG NAMES " + r_lib + "  PATHS ${" + lib_name + "_LIBRARIE_DIR_RELEASE} PATH_SUFFIXES lib) \n")

    file.writelines("# \n")
    file.writelines("   # merge debug and release libs to " + lib_name + "_LIBRARIES \n")
    file.writelines("   include(SelectLibraryConfigurations) \n")
    str_libs = ""
    for lib in debug_libs:
        file.writelines("   select_library_configurations( " + lib + " ) \n")
        str_libs = str_libs + " ${" + lib + "_LIBRARY} "
    file.writelines("   set(" + lib_name + "_LIBRARIES " + str_libs + ") \n")
    file.writelines("# \n")
    file.writelines("   include(FindPackageHandleStandardArgs) \n")
    file.writelines("   find_package_handle_standard_args(" + lib_name +" DEFAULT_MSG " + lib_name +"_INCLUDE_DIRS "+ lib_name +"_LIBRARIES_DIR "+ lib_name +"_LIBRARIES) \n")
    file.writelines("   mark_as_advanced("+ lib_name +"_INCLUDE_DIRS "+ lib_name + "_LIBRARIES_DIR  "+ lib_name +"_LIBRARIES) \n")
    file.writelines("endif() \n")
    file.close()


if __name__ == '__main__':
    generate_cmake_file(sys.argv[1:])
```

## 设置c++语言标准

```cmake
# 设置c++标准为11
set(CMAKE_CXX_STANDARD 11)
```

## 生成静态库或动态库

```cmake
cmake_minimum_required(VERSION 3.14)

project(add)

add_library(${PROJECT_NAME} STATIC add.h add.cpp)
# add_library(${PROJECT_NAME} SHARED add.h add.cpp)
```


## 系统平台判断

- 方法1

```cmake
if (CMAKE_SYSTEM_NAME matches "Linux")
	message(STATUS "current platform: Linux ")
elseif (CMAKE_SYSTEM_NAME matches "Windows")
	message(STATUS "current platform: Windows")
elseif (CMAKE_SYSTEM_NAME matches "FreeBSD")
	message(STATUS "current platform: FreeBSD")
else ()
	message(STATUS "other platform: ${CMAKE_SYSTEM_NAME}")
endif()
```

- 方法2

```cmake
if(WIN32)
    message(STATUS "current platform: Windows")
elseif(APPLE)

elseif(UNIX)

endif()
```

## 添加预处理器宏定义

```cmake
add_definitions(-DTEST -D_TEST)
```

## 设置Visual Studio字符集为Unicode

```cmake
if(WIN32)
    add_definitions(-DUNICODE -D_UNICODE)
endif()
```

## 设置Visual Studio子系统选项

- 如果你只是做一个直接的add_executable()命令，你将得到/SUBSYSTEM:CONSOLE 

- 如果你执行add_executable(exename WIN32 ...) ，你将获得/SUBSYSTEM:WINDOWS 

## qt相关

### windows环境变量设置

![setting_qt_dir](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-blog-imagessetting_qt_dir.png)

### 基础引用widget

### qml项目

```cmake
# 最低cmake版本要求
cmake_minimum_required(VERSION 3.14)

# 项目名称
project(qt_quick_demo)

# qt相关设置，设置moc、rcc、uic
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

# 包含当前目录
include_directories(./)

# 搜索qt core quick模块
find_package(Qt5 COMPONENTS Core Quick REQUIRED)

# 编译生成可执行文件
add_executable(${PROJECT_NAME} "main.cpp" "qml.qrc")

# 链接qt相关模块
target_link_libraries(${PROJECT_NAME}  Qt5::Core Qt5::Quick)
```

### 添加软件ico

```cmake
# cmake最低版本号要求
cmake_minimum_required(VERSION 3.14)

# 项目名称
project(add_ico_in_qt_demo)

# qt相关设置，设置moc、rcc、uic
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

# 包含当前目录
include_directories(./)
# 搜索Qt5
find_package(Qt5 COMPONENTS Widgets REQUIRED)

# 设置ico
if(WIN32)
    set(app_WINRC app_win32.rc)
    set_property(SOURCE app_win32.rc APPEND PROPERTY OBJECT_DEPENDS app_logo.ico)
endif()


# 编译生成可执行文件
add_executable(${PROJECT_NAME}
main.cpp 
mainwindow.cpp
mainwindow.ui
${app_WINRC}
)

# 链接qt库文件
target_link_libraries(${PROJECT_NAME}  Qt5::Widgets)
```

## 编译选项

```cmake
project(test)

option(TEST_DEBUG "option for debug" OFF)
if (TEST_DEBUG)
add_definitions(-DTEST_DEBUG)
endif()
# ...
```

```cpp
#include "test.h"

#ifdef TEST_DEBUG
//...
#endif
```

```bash
cmake -DTEST_DEBUG=ON .
cmake --build .
```

## 交叉编译

1. 编写toolchain.cmake

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

2. 构建

![配置](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-blog-imagescross_compile_configure.png)
![配置](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-blog-imagescross_compile_config_toolchain.png)



## 相关参考和学习资料

### 官方文档

[https://cmake.org/cmake/help/v3.20/](https://cmake.org/cmake/help/v3.20/)

### 中文手册

[https://www.zybuluo.com/khan-lau/note/254724](https://www.zybuluo.com/khan-lau/note/254724)