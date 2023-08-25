---
title: "vs2019编译opencv"
categories: [ "opencv" ]
tags: [ "opencv","vs2019","visual studio","编译","cmake" ]
draft: false
slug: "opencv/vs2019-compile-opencv"
date: "2019-06-10 14:57:00"
---

## 序

微软家的宇宙第一ide：visual studio已经更新到了2019版，芒果也更新尝鲜了一遍，体验还不错，建议更新尝尝鲜。芒果顺便使用vs2019编译了一遍opencv,编译过程也非常顺利，以下使用vs2019编译opencv的步骤。

## 编译过程

### 1.安装所需工具

本次编译的环境与工具为：

- windows10 64位 英文版（理论上win7以上的系统都可以）
- git（非必须，下载源代码使用）
- cmake (建议版本在3.15以上，芒果尝试过3.12无法构建vs2019的解决方案)
- vs2019最新版（2019.06.10）

软件安装过程芒果就不再赘述了，可以自行谷歌或者百度安装。**建议在安装git和cmake的时候将软件的环境变量添加到系统设置内（安装向导一般会有一个add ... to Path ...之类的选项，注意查看将选项勾上就ok)。**

### 2.下载源码

源码可以到github的opencv项目上可以直接下载，在github下载好代码压缩包解压就行。这里给出项目地址[opencv项目地址](https://github.com/opencv/opencv)。芒果这里直接使用git命令直接clone下来，如果你安装了git,也可以和芒果一样操作下载源码。

#### 首先新建一个OpenCVSourceCode文件夹，使用powershell（git需要添加环境变量）或者git bash打开。
输入命令
```powershell
 git clone https://github.com/opencv/opencv.git
```
然后等待下载完成,过程如下所示
```powershell
PS D:\OpenCVSourceCode> git clone https://github.com/opencv/opencv.git
Cloning into 'opencv'...
remote: Enumerating objects: 69, done.
remote: Counting objects: 100% (69/69), done.
remote: Compressing objects: 100% (57/57), done.
remote: Total 255212 (delta 13), reused 23 (delta 10), pack-reused 255143
Receiving objects: 100% (255212/255212), 458.96 MiB | 6.98 MiB/s, done.
Resolving deltas: 100% (178154/178154), done.
Checking out files: 100% (5851/5851), done.
PS D:\OpenCVSourceCode>
```
通过这种直接拉取opencv源码仓库的方式下载的是**最新**的opencv源码，也就是说此刻编译好的opencv将会是比官方发布最新版本还要新的专属最新版opencv。

### 3. cmake构建vs解决方案

#### cmake打开源码&设置构建方案路径

![cmake打开源码&设置构建方案路径][1]

如图，首先使用cmake打开下载的opencv源码，接着新建一个存放构建文件的文件夹，使用cmake打开。

#### 配置configure

![配置configure][2]

点击configure，如图选择2019的构建方案。

#### 再次configure

![再次configure][3]

点击配置configure一遍后，会出现红色的提示。是不是报错了呢，非也。不用慌，注意看会发现软件其实有提示的，只不过是英文的而已，按照提示再次点击一遍configure就好了。

![再次configure2][4]

再次配置后，红色提示就没了，此时如果注意看提示，正常的话就像途中的configuring done的提示。

#### 生成解决方案

![生成解决方案][5]

点击generate，正常生成后会有图中的提示。

```
configuring done
genereating done
```

### 4. vs编译解决方案

#### 打开解决方案

在上一步生成解决方案完成后，点击软件的Open Project就可以打开解决方案。注意电脑中如果有多个版本的vs,打开的时候注意选择vs2019打开。也可以到第一步设置构建文件路径哪里找到方案所在位置，一个名为OpenCV.sln的文件。

#### 生成All_BUILD

![生成All_BUILD][6]

打开解决方案后，找到All_BUILD项目，鼠标右键生成。等待一会后，即可编译完成。

![完成All_BUILD][7]

如图，正常编译完81个项目。

#### 生成INSTALL

右键生成INSTALL项目，完成后提示

![intall][8]

到此已经完成了编译过程。编译完成的文件，可以在install文件夹里找到。如图

![include][9]

打开install文件可以看到编译好的opencv.

![bin][10]

打开bin文件夹可以看到，动态链接库文件。

![lib][11]

lib文件夹为静态链接库文件。

可以注意到的是，这些链接库文件在opencv的版本号之后都带着一个字母d,这是debug的意思。因为咱们之编译了debug的版本，假如需要编译release版本，重新打开OpenCV.sln，修改项目为release再编译即可。
![release][12]

## 尾巴

按照步骤编译，整个过程应该非常顺利的，其实使用cmake编译第三方库都是一个通用的流程，总结下来就是归为：

- 1使用cmake打开源码，设置构建的vs解决方案.sln的文件路径
- 2配置configure构建的参数,一般按默认，直接点击configure就好，注意看提示，尤其是配置一遍还标红色的时候，往往会提示你再configure一遍。
- 3生成generate解决方案，同样直接点击就好
- 4打开解决解决方案，使用vs生成所有项目，再生成install项目。

---

本文由芒果浩明发布，转载需注明来源。
本文链接：https://blog.mangoroom.cn/opencv/vs2019-compile-opencv.html

  [1]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//vs2019-build-opencv-1.png
  [2]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//vs2019-build-opencv-2.png
  [3]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//vs2019-build-opencv-3.png
  [4]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//vs2019-build-opencv-4.png
  [5]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//vs2019-build-opencv-5.png
  [6]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//vs2019-build-opencv-6.png
  [7]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//vs2019-build-opencv-7.png
  [8]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//vs2019-build-opencv-8.png
  [9]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//vs2019-build-opencv-9.png
  [10]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//vs2019-build-opencv-10.png
  [11]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//vs2019-build-opencv-11.png
  [12]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//vs2019-build-opencv-12.png