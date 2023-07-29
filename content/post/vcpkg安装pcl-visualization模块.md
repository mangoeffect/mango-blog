---
title: "vcpkg安装pcl-visualization模块"
categories: [ "工具","PCL" ]
tags: [ "pcl","vcpkg","pcl-visualization","pcl-1.12.0" ]
draft: false
slug: "tools/vcpkg-install-pcl-visualization-module"
date: "2021-10-28 21:32:00"
---

自从发现vcpkg这好东西后，工作学习项目都转向vcpkg管理依赖了。在转换包含pcl依赖项目的时候发现了一个问题，在windows上面使用vcpkg安装pcl发现是缺少visualization模块的，这对程序调试非常不方便。就这个问题网上搜了一遍，发现有如下原因：

- pcl-visualization模块所依赖的vtk版本一致比较老旧，而vcpkg仓库列表中的vtk模块更新得比较频繁。所以目前的vcpkg在安装pcl的时候把visualization模块给禁掉了
- 最新版本pcl(1.12.0)以及更新了vtk新版本(9.0)的支持，但是vcpkg官方并未跟上更新，看github上面issue的讨论是pcl-1.12.0变化比较大。vcpkg考虑晚些时候再更新。

知道原因后，那么解决办法就有两个路线，一个是降低pcl版本,vtk版本。另一个是更新pcl版本至最新的pcl-1.12.0。本着用新不用旧的原则，加上github上发现有人已经fork了vcpkg后自行更新了pcl-1.12.0，并且在Windows平台测试使用正常，该作者也已经提交至vcpkg仓库请求合并。所以后续官方vcpkg也会更新，相当于使用该版本的port-version版本0版。也正好符合我的使用需求，话不多多说下面介绍安装步骤：

## 下载升级pcl新版的vcpkg仓库

使用powershell打开一个目录

```bash
git clone https://github.com/raahilsha-z/vcpkg.git
```

## 安装vcpkg

运行安装脚本

```bash
 .\vcpkg\bootstrap-vcpkg.bat
 ```

## search pcl

搜索查看pcl

```bash
PS E:\vcpkg_pcl_visualization\vcpkg> .\vcpkg.exe search pcl
pcl                      1.12.0#1         Point Cloud Library (PCL) is open source library for 2D/3D image and point...
pcl[cuda]                                 CUDA support for PCL
pcl[opengl]                               OpenGL support for PCL
pcl[openni2]                              OpenNI2 support for PCL
pcl[pcap]                                 PCAP support for PCL
pcl[qt]                                   Qt support for PCL
pcl[tools]                                Build PCL utilities
pcl[vtk]                                  VTK-Visualizations support for PCL
rpclib                   2.3.0            a RPC library for C++, providing both a client and server implementation. ...
The search result may be outdated. Run `git pull` to get the latest results.

If your library is not listed, please open an issue at and/or consider making a pull request:
    https://github.com/Microsoft/vcpkg/issues
PS E:\vcpkg_pcl_visualization\vcpkg>
```
可以看到，pcl版已更新至pcl-1.12.0。

## 安装pcl

安装pcl，注意features选择vtk要选上

```
.\vcpkg.exe install pcl[vtk]
#或 .\vcpkg.exe install pcl[vtk]:x64-windows
```

安装完毕后，到

```
E:\vcpkg_pcl_visualization\vcpkg\installed\x86-windows\include\pcl
# 或E:\vcpkg_pcl_visualization\vcpkg\installed\x64-windows\include\pcl
```
目录下查看，就可以发现visualiztion头文件已经包含在内。

---------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/tools/vcpkg-install-pcl-visualization-module.html
