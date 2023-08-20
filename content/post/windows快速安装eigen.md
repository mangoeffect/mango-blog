---
title: "windows快速安装eigen"
categories: [ "Eigen" ]
tags: [ "cmake","windows","eigen","eigen-3.4.0" ]
draft: false
slug: "Eigen/quick-install-eigen-on-windows"
date: "2021-09-23 00:17:00"
---

## 下载

到托管平台gitlab下载eigen最新发布源码，eigen发布地址:[https://gitlab.com/libeigen/eigen/-/releases](https://gitlab.com/libeigen/eigen/-/releases)

## cmake构建

解压源码，在解压目录同级新建build目录与install目录。打开cmake

- 1 选择解压的eigen源码目录
- 2 选择vs解决方案生成目录
- 3 点击configure初次配置
- 4 勾选安装目录为install目录，取消勾选build test
- 5 再次点击configure配置
- 6 点击generate生产解决方案
- 7 点击Open Project
- 8 打开解决方案后右键install项目生成
执行后install目录下生成eigen包含模板头文件

下载:https://mangoroom.lanzoui.com/idn2qudow2b 
密码:9n8m

-------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/eigen/quick-install-eigen-on-windows.html

