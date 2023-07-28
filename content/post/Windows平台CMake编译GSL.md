---
title: "Windows平台CMake编译GSL"
categories: [ "工具" ]
tags: [ "visual studio","cmake","GSL" ]
draft: false
slug: "use-cmake-build-gsl-on-windows"
date: "2020-04-28 22:40:00"
---

## 简介

介绍了开源科学计算库GSL(GNU Scientific Library)在Windows平台使用CMake以及Visual Studio的编译过程。

----

 GSL全称GNU Scientific Library，是一个被广泛使用的开源科学计算库。官网地址为[https://www.gnu.org/software/gsl/](https://www.gnu.org/software/gsl/)。从官网可以下载源码，但是源码的Makefile文件是基于linux平台的，在windows平台编译比较麻烦。一需要跨平台编译就想到了CMake，不过需要对应的CMakeLists文件才能编译，刚好这部分工作已经有人做了，在Github上有CMake版本的GSL。仓库地址为：[https://github.com/ampl/gsl](https://github.com/ampl/gsl)。

## 步骤

### （1）

下载包含CMakeLists文件的GSL源码，下载地址[https://github.com/ampl/gsl/releases](https://github.com/ampl/gsl/releases)

### （2）

使用CMake构建Visual Studio解决方案,CMake配置如下，先configurate，然后检查选项设置设置如下图
![CMake配置.png][1]

再次configure,接着generate生成vs解决方案，最后Open Project打开解决方案进行编译。

### （3）

使用Visual Studio打开解决方案，首先ALL_BUILD,再INSTALL。成功后就可以在设置的安装路径下看到编译生成的文件。
![编译生成.png][2]

### （4）

配置、测试GSL

和其他库一样，配置使用一个库引用好include、lib、bin目录就可以，由于GSL编译只生成静态链接库，所以只需要配置好include以及lib就可以。
![GSL配置.png][3]
![GSL配置2.png][4]
在网站随便到一段代码，进行测试，输出贝塞尔函数的值。

```c++
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

输出

```
J0(5) = -1.775967713143382642e-01
```
---------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/tools/use-cmake-build-gsl-on-windows.html

  [1]: https://mangoroom.cn/usr/uploads/2020/04/122212023.png
  [2]: https://mangoroom.cn/usr/uploads/2020/04/4038256655.png
  [3]: https://mangoroom.cn/usr/uploads/2020/04/2504860031.png
  [4]: https://mangoroom.cn/usr/uploads/2020/04/1815873250.png