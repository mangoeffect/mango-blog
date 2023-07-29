---
title: "pcl-vtk-9.x-viewer嵌入qt窗口显示"
categories: [ "c++","工具","PCL" ]
tags: [ "Qt","pcl","vtk","pcl-visualization","qvtkwidget" ]
draft: false
slug: "cpp/pcl-vtk9-x-viewer-hosted-on-qt-widget"
date: "2022-03-04 09:50:00"
---

## 简介

本文介绍基于新版vtk(9.0以后) pcl点云显示控件viewer嵌入到Qt窗体控件中的方法，在这之前首先需要准备的是

- 下载安装pcl-12.1或者最新的版本
- 下载pcl对应版本的vtk版本源码, pcl-1.12.1对应的vtk版本是vtk9.1(在3rdparty中可以查看)
- 下载安装Qt5.x(建议5.12)
- 下载安装CMake

本文实验在windows平台下测试通过

## 步骤

### 编译vtk

使用CMake编译vtk,注意以下选项设置

- VTK_GROUP_ENABLE_Qt 选项选择YES
- VTK_QT_VERSION 选择Yes即可(如果电脑安装有多个版本Qt，可以选择指定版本)
- CMAKE_INSTALL_PREFIX可以选择指定的安装目录，否则默认安装目录在C

其他设置按照默认的即可，在config与generate结束后，打开VS编译生成，等待编译结束后拷贝生成的文件，覆盖至pcl安装目录的3rdparty中的vtk。此时vtk已经包含了依赖Qt的模块了，但pcl的CMake配置文件中并未添加VTK中依赖Qt部分的模块，需要手动改一下添加。

在pcl安装目录的cmake目录中，默认的为

```
C:\Program Files\PCL 1.12.1\cmake
```
找到PCLConfig.cmake文件,495行左右找到

```cmake
set(PCL_VTK_COMPONENTS ...
```
一行，将其注释掉，修改为下面一行的内容
```cmake
# VTK components required by PCL

#set(PCL_VTK_COMPONENTS "ChartsCore;CommonColor;CommonComputationalGeometry;CommonCore;CommonDataModel;CommonExecutionModel;CommonMath;CommonMisc;CommonTransforms;FiltersCore;FiltersExtraction;FiltersGeneral;FiltersGeometry;FiltersModeling;FiltersSources;ImagingCore;ImagingSources;InteractionImage;InteractionStyle;InteractionWidgets;IOCore;IOGeometry;IOImage;IOLegacy;IOPLY;RenderingAnnotation;RenderingCore;RenderingContext2D;RenderingLOD;RenderingFreeType;ViewsCore;ViewsContext2D;RenderingOpenGL2")
set(PCL_VTK_COMPONENTS "ChartsCore;CommonColor;CommonComputationalGeometry;CommonCore;CommonDataModel;CommonExecutionModel;CommonMath;CommonMisc;CommonTransforms;FiltersCore;FiltersExtraction;FiltersGeneral;FiltersGeometry;FiltersModeling;FiltersSources;ImagingCore;ImagingSources;InteractionImage;InteractionStyle;InteractionWidgets;IOCore;IOGeometry;IOImage;IOLegacy;IOPLY;RenderingAnnotation;RenderingCore;RenderingContext2D;RenderingLOD;RenderingFreeType;ViewsCore;ViewsContext2D;RenderingOpenGL2;vtkGUISupportQt;vtkGUISupportQtQuick;vtkGUISupportQtSQL;vtkRenderingQt;vtkViewsQt")
```

至此完成了VTK依赖Qt的模块组件的添加。

### viewer嵌入Qt窗口

在就版本的VTK中已经支持pcl点云显示组件viewer的嵌入，但版本被限定在了vtk8.x以前，pcl对应也不能使用新的版本。以前嵌入的方式是基于vtk的qvtkwidget控件，但在vtk9.0以后qvtkwidget组件被弃用了，不能再使用。vtk提供了QVTKOpenGLNativeWidget作为替代，使用方法如下

```cpp
//widget定义为QVTKOpenGLNativeWidget类型窗口
auto renderer = vtkSmartPointer<vtkRenderer>::New();
auto renderWindow = vtkSmartPointer<vtkGenericOpenGLRenderWindow>::New();
renderWindow->AddRenderer(renderer);
viewer_.reset(new pcl::visualization::PCLVisualizer(renderer, renderWindow, "viewer", false));
ui_->widget->setRenderWindow(viewer_->getRenderWindow());
viewer_->setupInteractor(ui_->widget->interactor(), ui_->widget->renderWindow());
```

关键嵌入步骤程序代码为以上部分，其他与旧版本的pcl-viewer嵌入到qvtkwidget中并无差异。

-----------
本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/cpp/pcl-vtk9-x-viewer-hosted-on-qt-widget.html
