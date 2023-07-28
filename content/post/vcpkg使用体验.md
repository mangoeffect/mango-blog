---
title: "vcpkg使用体验"
categories: [ "c++","工具" ]
tags: [ "opencv","cmake","vs","vcpkg" ]
draft: false
slug: "feedback-of-use-vcpkg"
date: "2021-10-25 22:14:00"
---

最近被朋友安利了一个好东西，体验下来发现真不错。解决了C++开发过程中很多麻烦的地方，使用几天下来就决定以后都用它做项目开发了。这是一个用于获取和管理第三方C++库，统一管理项目库文件依赖的工具，由微软开发，并开源在了github上面。仓库地址[https://github.com/microsoft/vcpkg](https://github.com/microsoft/vcpkg)。
![vcpkg.jpg][1]
## vcpkg简介

这是一个C++的包管理工具，它的出现就是为了解决C++项目依赖在不同系统不同开发工具相互不兼容，不通用的问题。相比之前的CMake,Makefiles与Qmake或者VS等管理工具，vcpkg具有以下优点

- 跨windows、macos、linux平台
- 自动下载开源库源代码，从github下载官方源码
- 源码包的缓存管理和版本管理，可以升级版本。类似python的pip或者conda之类的
- 一键轻松编译，相关的编译过程vcpkg都使用powershell脚本等写好了，实现了一个命令行安装一个库的体验
- 依赖关系检查（比如编译pcl，会自动下载eigen、boost 进行编译）。
- 无缝集成 Visual Studio，不需要设置库文件、头文件的所在目录，自动集成，再也不需要配置属性表了。
- Visual Studio 全平台支持，不仅支持 Debug/Release、x86/x64 编译，还支持 UWP、ARM 平台的编译
- 自动拷贝依赖库文件至调试目录,开发完软件再也不需要一个一个去查找哪些动态库需要拷贝一起发布

## 安装vcpkg

安装过程也非常简单，vcpkg针对三个平台都写了一键安装的脚本。需要注意的是资源都从国外网站下载，部分地区或者晚上繁忙时段可能会下载失败，最好准备一个梯子。

### windows

使用powershell打开一个目录，官方建议C:/src

```
> git clone https://github.com/microsoft/vcpkg
> .\vcpkg\bootstrap-vcpkg.bat
```

### linuxs、macos

```
$ git clone https://github.com/microsoft/vcpkg
$ ./vcpkg/bootstrap-vcpkg.sh
```

## 安装库

首先，使用search命令搜索vcpkg是否支持该库文件的安装，以及安装选项等信息。以安装opencv为例

```
PS C:\src\vcpkg> .\vcpkg.exe search opencv
darknet[opencv-base]                      Build darknet with support for latest version of OpenCV
darknet[opencv-cuda]                      Build darknet with support for latest version of CUDA-enabled OpenCV
darknet[opencv2-base]                     Build darknet with support for OpenCV2
darknet[opencv2-cuda]                     Build darknet with support for CUDA-enabled OpenCV2
darknet[opencv3-base]                     Build darknet with support for OpenCV3
darknet[opencv3-cuda]                     Build darknet with support for CUDA-enabled OpenCV3
matplotplusplus[opencv]                   opencv support for Matplot++
opencv                   4.5.3            Computer vision library
opencv[ade]                               graph api
opencv[contrib]                           opencv_contrib module
opencv[cuda]                              CUDA support for opencv
opencv[cudnn]                             cuDNN support for opencv
opencv[dnn]                               Enable dnn module
opencv[eigen]                             Eigen support for opencv
opencv[ffmpeg]                            ffmpeg support for opencv
opencv[gdcm]                              GDCM support for opencv
opencv[halide]                            Halide support for opencv
opencv[ipp]                               Enable Intel Integrated Performance Primitives
opencv[jasper]                            JPEG 2000 support for opencv
opencv[jpeg]                              JPEG support for opencv
opencv[lapack]                            LAPACK support for opencv
opencv[nonfree]                           opencv nonfree module
opencv[openexr]                           OpenEXR support for opencv
opencv[opengl]                            opengl support for opencv
opencv[openmp]                            Enable openmp support for opencv
opencv[ovis]                              opencv_ovis module
opencv[png]                               PNG support for opencv
opencv[qt]                                Qt GUI support for opencv
opencv[quirc]                             Enable QR code module
opencv[sfm]                               opencv_sfm module
opencv[tbb]                               Enable Intel Threading Building Blocks
opencv[tiff]                              TIFF support for opencv
opencv[vtk]                               vtk support for opencv
opencv[webp]                              WebP support for opencv
opencv[world]                             Compile to a single package support for opencv
opencv2                  2.4.13.7#6       computer vision library
opencv2[cuda]                             CUDA support for opencv
opencv2[eigen]                            Eigen support for opencv
opencv2[ffmpeg]                           ffmpeg support for opencv
opencv2[jasper]                           JPEG 2000 support for opencv
opencv2[jpeg]                             JPEG support for opencv
opencv2[openexr]                          OpenEXR support for opencv
opencv2[opengl]                           opengl support for opencv
opencv2[png]                              PNG support for opencv
opencv2[qt]                               Qt GUI support for opencv
opencv2[tiff]                             TIFF support for opencv
opencv2[world]                            Compile to a single package support for opencv
opencv3                  3.4.15#3         computer vision library
opencv3[contrib]                          opencv_contrib module
opencv3[cuda]                             CUDA support for opencv
opencv3[dnn]                              Enable dnn module
opencv3[eigen]                            Eigen support for opencv
opencv3[ffmpeg]                           ffmpeg support for opencv
opencv3[flann]                            opencv_flann module
opencv3[gdcm]                             GDCM support for opencv
opencv3[halide]                           Halide support for opencv
opencv3[ipp]                              Enable Intel Integrated Performance Primitives
opencv3[jasper]                           JPEG 2000 support for opencv
opencv3[jpeg]                             JPEG support for opencv
opencv3[lapack]                           LAPACK support for opencv
opencv3[nonfree]                          allow nonfree and unredistributable libraries
opencv3[openexr]                          OpenEXR support for opencv
opencv3[opengl]                           opengl support for opencv
opencv3[ovis]                             opencv_ovis module
opencv3[png]                              PNG support for opencv
opencv3[qt]                               Qt GUI support for opencv
opencv3[quirc]                            Enable QR code module
opencv3[sfm]                              opencv_sfm module
opencv3[tbb]                              Enable Intel Threading Building Blocks
opencv3[tiff]                             TIFF support for opencv
opencv3[vtk]                              vtk support for opencv
opencv3[webp]                             WebP support for opencv
opencv3[world]                            Compile to a single package support for opencv
opencv4                  4.5.3#3          computer vision library
opencv4[ade]                              graph api
opencv4[contrib]                          opencv_contrib module
opencv4[cuda]                             CUDA support for opencv
opencv4[cudnn]                            cuDNN support for opencv
opencv4[dnn]                              Enable dnn module
opencv4[eigen]                            Eigen support for opencv
opencv4[ffmpeg]                           ffmpeg support for opencv
opencv4[gdcm]                             GDCM support for opencv
opencv4[halide]                           Halide support for opencv
opencv4[ipp]                              Enable Intel Integrated Performance Primitives
opencv4[jasper]                           JPEG 2000 support for opencv
opencv4[jpeg]                             JPEG support for opencv
opencv4[lapack]                           LAPACK support for opencv
opencv4[nonfree]                          allow nonfree and unredistributable libraries
opencv4[openexr]                          OpenEXR support for opencv
opencv4[opengl]                           opengl support for opencv
opencv4[openmp]                           Enable openmp support for opencv
opencv4[ovis]                             opencv_ovis module
opencv4[png]                              PNG support for opencv
opencv4[qt]                               Qt GUI support for opencv
opencv4[quirc]                            Enable QR code module
opencv4[sfm]                              opencv_sfm module
opencv4[tbb]                              Enable Intel Threading Building Blocks
opencv4[tiff]                             TIFF support for opencv
opencv4[vtk]                              vtk support for opencv
opencv4[webp]                             WebP support for opencv
opencv4[world]                            Compile to a single package support for opencv
openimageio[opencv]                       Enable opencv support for openimageio
openmvg[opencv]                           opencv support for openmvg
selene[opencv]                            Enable using OpenCV
zxing-cpp[opencv]                         Build with opencv
The search result may be outdated. Run `git pull` to get the latest results.

If your library is not listed, please open an issue at and/or consider making a pull request:
    https://github.com/Microsoft/vcpkg/issues
PS C:\src\vcpkg>
```
可以看到，包含opencv安装的有一些的信息，这些信息说明你可以选择安装opencv的版本、是否包含openmp、tbb等。如：

```
# 安装默认版本的opencv，32bit
./vcpkg.exe intall opencv
# 安装并行框架为openmp
./vcpkg.exe intall opencv[openmp]
# 多个选项
./vcpkg.exe intall opencv[openmp,qt]
# 安装windows 64位版本
./vcpkg.exe intall opencv[openmp]:x64-windows
```

## 集成CMake使用

仍以opencv为例，按照上一步骤安装完成后，无需设置opencv的环境变量，项目CMakeLists.txt文件也不需要修改，只需要在cmake的时候设置
```
-DCMAKE_TOOLCHAIN_FILE=<vcpkg_dir>/scripts/buildsystems/vcpkg.cmake
```
比如
```
# cmake -G "Visual Studio 16 2019" .. #原命令
cmake -G "Visual Studio 16 2019" .. -DCMAKE_TOOLCHAIN_FILE=<vcpkg_dir>/scripts/buildsystems/vcpkg.cmake
```
即可，构建生成VS解决方案后，编译项目时，vcpkg会自动将依赖的动态库拷贝至VS的调试目录。

## 国内网络缓慢问题

使用一段时间下来，感觉vcpkg的使用体验时非常良好的，毕竟明显的一个槽点就是国内使用受网络环境影响比较严重，时常容易出现下载安装失败的情况。这个问题目前有两个办法解决吧，

- 准备梯子，在下载安装的时候直接开启全局代理模式
- 使用国内镜像站

由于vcpkg国内使用人数还比较少，还没有官方的镜像站点或者清华大学类型的教育机构镜像站点，目前只有一个vcpkg国内贡献者自建的一个镜像站点。但站点带宽有点小，没有梯子的朋友可以去看一下，总比直接从外网下载要好的。
[vcpkg国内源](https://zhuanlan.zhihu.com/p/383683670)


-----

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/cpp/feedback-of-use-vcpkg.html

  [1]: https://mangoroom.cn/usr/uploads/2021/10/462147729.jpg