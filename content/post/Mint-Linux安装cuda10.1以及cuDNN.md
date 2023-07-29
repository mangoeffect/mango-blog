---
title: "Mint-Linux安装cuda10.1以及cuDNN"
categories: [ "计算机视觉" ]
tags: [ "cuda","linux" ]
draft: false
slug: "计算机视觉/install-cuda-and-cudnn-on-mint-linux"
date: "2020-03-18 09:54:00"
---

本文介绍了Mint-Linux系统下的cuda10.1以及cuDNN安装过程，跟着芒果下文的安装步骤你可以顺利地安装cuDNN环境。

----

自从毕设之后，由于工作的原因再也没有碰过深度学习了。最近在工作闲暇的疫情时期思考了一番工作的规划，仔细考虑后还是需要多学点东西，得把深度学习重新学起来。于是就动手买了一块RTX2060搭建自己的深度学习环境。

----

关于系统的选择上，网上逛了一圈，发现对于DL比较友好还是Debian系，于是我在Ubuntu和Mint之间选了Ubuntu（主要是毕设的用Ubuntu的时候经历过不友好的体验）。

----

至于为什么选择安装cuda10.1的版本，因为逛了一下tensorflow和pytorch发现两个字主流的深度学习框架最新的支持都是cuda10.1而没有支持最新的10.2。
![CUDA.png][1]

## 步骤

下面开始安装，为了避免出什么岔子，以下的安装步骤芒果均参考官方指示

### 官网下载cuda10.1

官网下载地址：
[链接](https://developer.nvidia.com/cuda-10.1-download-archive-base?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal)

因为Mint是基于Ubuntu开发的，而Mint19是基于Ubuntu18.04LTS开发的，所以以Ubuntu18.04为准选择软件安装包，如下图：
![CUDA Toolkit 10 1 original Archive.png][2]

### 安装cuda10.1

如果你稍加注意就可以发现在你下载的同时官网已经给出了安装指导，安装指导在下载地址下方的Base Installer:

```bash
# step1
sudo dpkg -i cuda-repo-ubuntu1804-10-1-local-10.1.105-418.39_1.0-1_amd64.deb
# step2
sudo apt-key add /var/cuda-repo-<version>/7fa2af80.pub
# step3
sudo apt-get update
# step4
sudo apt-get install cuda
```
按照以上的步骤安装至完成，期间如果你的显卡驱动与cuda10.1的不匹配，安装程序会为你自动安装替换，芒果的原先为435的驱动安装完成后被替换成430。
安装结束后，最好先重启一下系统。

### 官网下载cuDNN

官网下载地址：
[链接](https://developer.nvidia.com/rdp/cudnn-download)

同样地我们以Ubuntu18.04来选择软件包版本，下载三个软件包：运行时的Runtime、开发链接库的Developer Library以及示例Samples。
![cuDNN Download.png][3]

这里需提醒一下，一个一个地下载，因为芒果就一起下载发现碰到了诡异的问题，三个一起下载后安装的时候发现安装包会报错说软件包已损坏。

### 安装cuDNN

下载的安装包为Debian系的.deb打包格式，下面就以命令行的方式安装

```bash
# step1
sudo dpkg -i libcudnn7-dev_7.6.5.32-1+cuda10.1_amd64.deb
# step2
sudo dpkg -i libcudnn7_7.6.5.32-1+cuda10.1_amd64.deb
# step3
sudo dpkg -i libcudnn7-doc_7.6.5.32-1+cuda10.1_amd64.deb
```


### 测试

将```/usr/src/cudnn_samples_v7/```复制到有写权限的目录下，并编译

```bash
sudo cp /usr/src/cudnn_samples_v7/ .
cd cd cudnn_samples_v7/mnistCUDNN/
make
```
如果编译成功则说明已经成功安装了cuda以及cuDNN开发环境。如果过程中出现了**cannot find -lcudnn××**的错误，可能是没安装g++编译器（芒果新装的系统就碰到了），先安装g++后在重新安装就可以了

```bash
sudo apt install g++
```
## references

[【1】developer.nvidia.com](https://developer.nvidia.com/cuda-10.1-download-archive-base?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal)
[【2】Linux Mint 19安装Cuda 9.2以及cuDNN、NCCL ](http://www.booksword.info/2018/07/14/cuda-9-2-installation-on-Linux-mint-19/)

------
本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/computer-vision/install-cuda-and-cudnn-on-mint-linux.html

  [1]: https://mangoroom.cn/usr/uploads/2020/03/2653844065.png
  [2]: https://mangoroom.cn/usr/uploads/2020/03/3707101071.png
  [3]: https://mangoroom.cn/usr/uploads/2020/03/1047898128.png