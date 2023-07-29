---
title: "windows平台msvc编译intel-tbb"
categories: [ "c++","工具" ]
tags: [ "c++","cmake","windows","intel","tbb","thread" ]
draft: false
slug: "cpp/build-intel-tbb-with-msvc-on-windows"
date: "2021-05-07 22:04:00"
---

![intel-tbb-title.png][1]
## 下载源码

tbb是有开源版和商业版，开源版代码放在了github上面。到仓库的release页面可用下载源码

[https://github.com/oneapi-src/oneTBB/releases](https://github.com/oneapi-src/oneTBB/releases)

选择最新的发布版本即可

## CMake构建

这一步使用CMake构建VS解决方案，可以使用命令行或者GUI形式。

### 命令行模式

命令行模式需要将cmake配置到环境变量，在目录下打开powershell或者gitbash控制台。

```bash
# 新建build目录存放VS解决方案
mkdir build
cd build
cmake -G "Visual Studio 16 2019" ../oneTBB-2021.2.0
```

执行命令后，如有以下配置和构建结束的提示，说明已经构建成功。

```bash
PS F:\3rdparty\TBB\build> cmake -G "Visual Studio 16 2019" ../oneTBB-2021.2.0
-- The CXX compiler identification is MSVC 19.28.29914.0
-- Check for working CXX compiler: C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.28.29910/bin/Hostx64/x64/cl.exe
-- Check for working CXX compiler: C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.28.29910/bin/Hostx64/x64/cl.exe - works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Looking for C++ include pthread.h
-- Looking for C++ include pthread.h - not found
-- Found Threads: TRUE
-- HWLOC target HWLOC::hwloc_1_11 doesn't exist. The tbbbind target cannot be created
-- HWLOC target HWLOC::hwloc_2 doesn't exist. The tbbbind_2_0 target cannot be created
-- HWLOC target HWLOC::hwloc_2_4 doesn't exist. The tbbbind_2_4 target cannot be created
-- The C compiler identification is MSVC 19.28.29914.0
-- Check for working C compiler: C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.28.29910/bin/Hostx64/x64/cl.exe
-- Check for working C compiler: C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.28.29910/bin/Hostx64/x64/cl.exe - works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: F:/3rdparty/TBB/build
PS F:\3rdparty\TBB\build>
```

### GUI模式

GUI模式则使用CMake软件程序完成上述构建，遵循以下截图步骤操作即可
![cmake-select-directories.png][2]

![cmake-config.png][3]

![cmake-config-2.png][4]

![cmake-config-done.png][5]

![cmake-build-done.png][6]


## VS编译安装

在上一步使用CMake完成构建后会生成VS解决方案，使用VS打开，
![vs-buill.png][7]
编译
![vs-install.png][8]
安装
![vs-release.png][9]
切换release重复编译-安装步骤。
![install.png][10]
安装完成后，安装目录下已生成相关库文件。

---------
本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/cpp/build-intel-tbb-with-msvc-on-windows.html

  [1]: https://mangoroom.cn/usr/uploads/2021/05/2976775110.png
  [2]: https://mangoroom.cn/usr/uploads/2021/05/427069888.png
  [3]: https://mangoroom.cn/usr/uploads/2021/05/3166162117.png
  [4]: https://mangoroom.cn/usr/uploads/2021/05/1320950418.png
  [5]: https://mangoroom.cn/usr/uploads/2021/05/1674430728.png
  [6]: https://mangoroom.cn/usr/uploads/2021/05/2390419373.png
  [7]: https://mangoroom.cn/usr/uploads/2021/05/1991396510.png
  [8]: https://mangoroom.cn/usr/uploads/2021/05/1601160449.png
  [9]: https://mangoroom.cn/usr/uploads/2021/05/3284086116.png
  [10]: https://mangoroom.cn/usr/uploads/2021/05/428159166.png