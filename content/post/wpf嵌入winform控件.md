---
title: "wpf嵌入winform控件"
categories: [ "DotNet" ]
tags: [ "c#","wpf","winform" ]
draft: false
slug: "DotNet/wfp-import-winform-control"
date: "2019-08-15 19:42:00"
---

实现地方法思路可以简单地概括为：在wpf设计界面放置一个容器，在后台c#代码新建winform控件，将其添加进容器。此方法支持嵌入标准库的控件以及自定义或者第三方封装好的winform控件。方法流程如下：

## 1、引入DLL

首先要引用两个winform相关的dll.

- WindowsFormsIntegration.dll
- System.Windows.Forms.dll

以上两个dll是必须引入的，另外，如果需要嵌入的控件已经封装成dll，那么也需要手动引入。

## 2、在xmal中添加命名空间

```xml
xmlns:wfi="clr-namespace:System.Windows.Forms.Integration;assembly=WindowsFormsIntegration"
```
这个命名空间是必须的，如果界面嵌入标准库控件，则添加以下两个命名空间

```xml
xmlns:wfi ="clr-namespace:System.Windows.Forms.Integration;assembly=WindowsFormsIntegration" 
        xmlns:wf ="clr-namespace:System.Windows.Forms;assembly=System.Windows.Forms"
```

## 3、在xmal设计界面添加宿主容器

添加标准库容器与控件方法如下

```xml
<!--PictureBox为例子-->
<Grid HorizontalAlignment="Left" Height="482" Margin="6,6,0,0" VerticalAlignment="Top" Width="737">
            <wfi:WindowsFormsHost>
                <wf:PictureBox x:Name="Cv_Main" Margin="0,0,0,0"></wf:PictureBox>
            </wfi:WindowsFormsHost>
</Grid>
```
添加第三方控件，则只需要在设计界面放置宿主容器,但必须命名

```xml
<Grid HorizontalAlignment="Left" Height="482" Margin="6,6,0,0" VerticalAlignment="Top" Width="737">
            <wfi:WindowsFormsHost
                x:Name="WinFormHost">
                <!--留空-->
            </wfi:WindowsFormsHost>
</Grid>
```

然后再后台代码添加，方法如下

```c#
using YourControlNamespace;
YourControl control = new YourControl();
this.WinFormHost.Child = control;//宿主容器必须命名，否则后台找不到宿主添加
```
按照上面的方法，修改自己控件的命名空间以及名字。至此，便可顺利将winform控件成功嵌入wpf。

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/donet/wfp-import-winform-control.html

