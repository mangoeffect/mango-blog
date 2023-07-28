---
title: "vs离线安装NuGet程序包"
categories: [ "DotNet" ]
tags: [ "vs2019","visual studio","NuGet","c#","wpf" ]
draft: false
slug: "vs-install-local-nuget-package"
date: "2019-08-11 16:39:00"
---

## NuGet

先介绍一下NuGet。NuGet是一个自由的、开源的软件包管理系统，用于微软开发平台，属于visual studio的一个扩展。它的作用是简化了vs项目中添加、更新和删除第三方程序库的工作，NuGet包是以.nupkg扩展名的，NuGet以前称为NuPack。
一开始芒果以为NuGet只是支持.net平台得c#之类的程序包，了解后才发现其实NuGet支持多种语言的程序包，比如哪里都有的c++。

## 安装

采用NutGet安装和管理程序包有在线和离线安装的方式。在线就不在这里赘述，方法非常简单，在vs中直接搜索安装就可以。安装方法步骤为

1. 工具
2. NuGet包管理器
3. 管理解决方案的NuGet程序包
4. 浏览-搜索,输入相应的程序包名称
5. 点击安装

## 离线安装

离线安装的方法也非常的简单，区别就是安装的软件包是自己下载的，不是从Vs的NeGet包程序商店里下载的而已。安装步骤如下

1. 自己下载NuGet格式的程序安装包。这里推荐两种方式下载，一是到NuGet官网
网址为https://www.nuget.org/。而是到该软件包的官方网址或者github的release中，比如MaterialDesignInXamlToolkit的安装包
https://github.com/MaterialDesignInXAML/MaterialDesignInXamlToolkit/releases
2. 工具
3. NuGet包管理器
4. 管理解决方案的NuGet程序包
5. 设置
6. 添加可用程序程序包源
7. 命名本地离线的程序包源，选择存放有nupkg格式程序包的文件夹路径
8. 选中新建的离线程序包源，浏览
9. 安装

![新建本地可用程序源.png][1]


---


本文由芒果浩明发布，转载请注明来源。
本文链接：https://mangoroom.cn/donet/vs-install-local-nuget-package.html

  [1]: https://mangoroom.cn/usr/uploads/2019/08/2594341301.png