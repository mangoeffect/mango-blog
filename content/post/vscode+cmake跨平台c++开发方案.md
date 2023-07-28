---
title: "vscode+cmake跨平台c++开发方案"
categories: [ "工具" ]
tags: [ "c++","cmake","vscode","cross-platform" ]
draft: false
slug: "cpp-cross-platform-develop-sulotion-based-vscode-and-cmake"
date: "2021-11-16 21:47:00"
---

## 简介

本文介绍一种基于VS Code、CMake与C/C++ Extension Pack扩展包的C++跨平台方案，该方案具有不通平台编码开发体验一致的效果，可以认为是免费轻量化的clion开发体验，在满足跨平台开发的同时也方便了使用VScode其他强大的扩展插件。

## C++跨平台开发方案

由于与硬件、系统相关性比较强的原因，虽然说C++是一门跨平台的编程语言，但是跨平台的开发体验相比python，java等还是差了不少。一般常见的C++跨平台开发方案有：

### CodeBlock

这是基于codeblock本身是跨平台的一个ide，优点是轻量跨平台，启动速度快。

----

缺点有界面主题比较少，插件非常少，语法提示不够智能，更新缓慢,未集成CMake的支持，windows平台默认mingw，msvc编译器需要手动配置。

### Qt Creator

Qt Creator为Qt的官方开发工具，提供Qt的跨平台开发支持。优点有轻量跨平台，启动速度快。默认集成CMake支持以及Qt自己的QMake支持，方便开发Qt程序，windows平台多编译器切换方便。

-----

缺点是插件比较少，界面主题偏少，更新也比较慢。

### Clion

Cloin是JetBrains公司开发的一款跨屏的的C++IDE，优点是界面美观功能强大，默认CMake管理项目，语法提示友好。JetBrains开发的工具风格比较统一，风评比较好。

-----

缺点是稍慢(可能与基于Java开发的有关系)，对于性能不佳的机子可能优点吃力，软件是付费的(学生邮箱可以获得免费版使用资格)。

### CMake

还有一种方案是直接使用编辑器或者记事本编写代码和CMakeLists.txt，然后再不同平台生成不同平台的编译方案，比如windows平台就生成Visual Studio解决方案，然后再用VS进行后续编辑开发和调试。再linux平台就直接生成makefile,然后使用gdb调试。这种方案的优点是项目工程只关注代码和CMakeLists.txt，对于各个平台选择何种编码与调试方式无限制，这在团队协同开发中可以照顾不同开发者的编码习惯。而本文介绍的方案正是基于这一方案，结合了非常受欢迎的VScode编辑器，将编码工程集成到VScode中去，利用VScode非常好的编码体验，同时也拥有一定的调试功能。

## VS code + cmake +  VS code + C/C++ Extension Pack扩展方案

来到本文采用的方案，VS code + cmake +  VS code + C/C++ Extension Pack扩展方案，该方案具有以下的优点：

- cmake集成，cmake管理C++项目是现在主流的方案，cmake语法智能提示
- VScode编码，可以享受VScode美观的界面、快速启动、智能提示与其他各种强大的宽展插件
- 各个平台一致的编码、调试体验
- 快速切换不通编译器
- 代码工程可以无缝切换到其他开发工具去

----

当然，这套方案缺点是调试功能方面相比clion要差一些。下面介绍方案搭建步骤：

### 安装VScode

到官网下载对应平台安装包，安装即可

[https://code.visualstudio.com/download](https://code.visualstudio.com/download)

### 安装编译器

C++编译器众多，推荐各个对台采用该平台的主流编译器方案，这里推荐

- Windows平台安装cl编译器，安装Visual Studio即可
- Linux平台安装g++编译器，一般系统(ubuntu)已经自带，若没有就```sudo apt install g++```命令安装即可
- MacOS平台安装clang编译器，笔者没有MacOS，这里就麻烦MacOS的朋友自行解决

### 安装C/C++ Extension Pack扩展包

打开VSCode，点击左侧扩展图表，搜索```C/C++ Extension Pack```扩展，点击进去，发现这是一个扩展包集合，里面包含了多个扩展插件，这是微软推荐的C++开发扩展工具，点击一键安装。

![cpp-extension-pack.png][1]
### 编码体验测试

以下以windows平台为例，介绍本方案的编码开发体验

#### 编写CMakeLists.txt

首先，在工程目录下新建CMakeLists.txt, 与一个main.cpp文件暂时不用编码，CMakeLists.txt用VScode打开编写简单的构建脚本，内容如下:

```cmake
cmake_minimum_required(VERSION 3.14)

project(demo)

add_executable(demo main.cpp)
```
![cmake语法提示.gif][2]
可以看到编写cmake的时候还有智能提示

#### 构建项目

接下来，使用VScode打开工程目录，点击右侧cmake扩展，再点击构建按钮

![cmake构建.gif][3]
可以发现如果你安装了多个编译器，再第一次构建的时候会提示你选择哪个编译器进行构建，也可以选择编译的位数。这实际上就是一次cmake -G的过程，所以第一次生成往往耗时比较长，第二次构建就比较快了。生成项目后，左侧会出现和VS一样的项目列表，之后你就可以进行编码工作了。比如main.cpp代码内容为：

```cpp
#include <iostream>

int main(int argc, char** argv)
{
    std::cout << "Hello World!"<< std::endl;
    return 0;
}
```

编写完成后，可以想VS一样右键项目选择编译或者调试项目
![编译运行.gif][4]
### 断点调试

除了编译运行，还支持简单的断点调试，与VS快捷键一样，按F9插入取消断点，F5进行调试运行。

![断点调试.gif][5]
倘若要进行复杂的调试，这时候打开工程目录下生成的build目录，点击进去，你会发现里面已经生成了一个VS解决方案，直接使用VS打开就可以进行后续开发和调试工作。
![build目录.png][6]

![VS解决方案.png][7]

--------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/tools/cpp-cross-platform-develop-sulotion-based-vscode-and-cmake.html


  [1]: https://mangoroom.cn/usr/uploads/2021/11/3283257012.png
  [2]: https://mangoroom.cn/usr/uploads/2021/11/1299160451.gif
  [3]: https://mangoroom.cn/usr/uploads/2021/11/1613595199.gif
  [4]: https://mangoroom.cn/usr/uploads/2021/11/3582277633.gif
  [5]: https://mangoroom.cn/usr/uploads/2021/11/1088792569.gif
  [6]: https://mangoroom.cn/usr/uploads/2021/11/3493808769.png
  [7]: https://mangoroom.cn/usr/uploads/2021/11/1961865065.png