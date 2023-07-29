---
title: "opencv学习之等待按键事件-waitKey函数"
categories: [ "opencv" ]
tags: [ "opencv","c++","waitKey" ]
draft: false
slug: "opencv/opencv-learning-waitKey"
date: "2019-06-07 22:57:00"
---

## 序

waitKey函数属于opencv函数里既常用又非常基础的函数，无论是刚开始学习opencv，还是使用opencv进行开发调试，都可以看到waitKey函数的身影。然而最基础的东西可能往往容易忽略掉，在此可以好好了解一遍这个基础又常用的waitKey函数。

## waitKey函数原型

翻阅opencv的官方文档，可以查阅到该函数的原型为

```

int cv::waitKey	(	int 	delay = 0	)	
Python:
retval	=	cv.waitKey(	[, delay]	)
```

从函数的原型可以了解到该函数以下几点的信息

- waitKey函数属于cv命名空间
- 一个int类型的参数，默认值为0，根据delay的名称可以猜测参数值是一个延时值
- 函数返回值为int类型

以上就是通过函数原型可以解读到的信息了，关于waitKey函数的更多，往下继续了解。

## waitKey函数详解

同样地，继续参考权威的opencv官方文档，文档对waitKey函数的解释有

- 1

> Waits for a pressed key.

**等待一个按键。** 可以理解为此函数的功能是等待一个按键按下。

- 2
> The function waitKey waits for a key event infinitely (when delay≤0 ) or for delay milliseconds, when it is positive. Since the OS has a minimum time between switching threads, the function will not wait exactly delay ms, it will wait at least delay ms, depending on what else is running on your computer at that time. It returns the code of the pressed key or -1 if no key was pressed before the specified time had elapsed.

**以上芒果理解为：**
函数 Waitkey 在参数delay为正整数n时，延迟n毫秒,或者无限等待按键事件 (delay≤0时) 。由于操作系统在切换线程之间需要时间, 该函数不会等待完全延迟n ms, 它将等待至少延迟n ms, 这具体取决于当时计算机上运行的其他时间。如果在指定的时间之内没有按下键, 则返回按下的键或-1 的ascii码。函数的返回值是键盘按键键值的ascii码。

- 3
> This function is the only method in HighGUI that can fetch and handle events, so it needs to be called periodically for normal event processing unless HighGUI is used within an environment that takes care of event processing.

此函数是 HighGUI 中唯一可以提取和处理事件的方法, 因此需要定期调用它进行正常的事件处理, 除非在处理事件处理的环境中使用 HighGUI。

- 4

> The function only works if there is at least one HighGUI window created and the window is active. If there are several HighGUI windows, any of them can be active.

该函数仅在至少创建了一个 HighGUI 窗口并且该窗口处于活动状态时才有效。如果有多个 HighGUI 窗口, 则其中任何一个都可以处于活动状态。

- 5
> delay	Delay in milliseconds. 0 is the special value that means "forever".

延迟延迟以毫秒为单位。0是表示 "永远" 的特殊值。即参数值为0时，waitKey函数等待的时间是无限长。

------

通过以上对官方文档的学习，对waitKey的认识可以归为:**waitKey函数是一个等待键盘事件的函数，参数值delay<=0时等待时间无限长，delay为正整数n时至少等待n毫秒的时间才结束。在等待的期间按下任意按键时函数结束，返回按键的键值（ascii码），等待时间结束仍未按下按键则返回-1。该函数用在处理HighGUI窗口程序，最常见的便是与显示图像窗口imshow函数搭配使用**

## waitKey函数用法

waitKey函数非常基础以及常用，以下是常见的一些用法

- 1
```c++
cv::imshow("windowname", image);
cv::waitKey(0);//任意按键按下，图片显示结束，返回按键键值
```

- 2
```c++
cv::imshow("windowname", image);
cv::waitKey(10);//等待至少10ms图片显示才结束，期间按下任意键图片显示结束，返回按键键值
```

- 3
```c++
 VideoCapture cap("video.mp4"); 
    if(!cap.isOpened()) 
    { 
        return -1; 
    } 
    Mat frame;  
    while(true) 
    { 
        cap>>frame; 
        if(frame.empty()) break;
        imshow("windowname",frame); 
        if(waitKey(30) >=0) //延时30ms,以正常的速率播放视频,播放期间按下任意按键则退出视频播放，并返回键值
            break;
    } 
```

- 4

```c++
 VideoCapture cap("video.mp4"); 
    if(!cap.isOpened()) 
    { 
        return -1; 
    } 
    Mat frame;  
    while(true) 
    { 
        cap>>frame; 
        if(frame.empty()) break;
        imshow("windowname",frame); 
        if(waitKey(30) == 27) //延时30ms,以正常的速率播放视频,播放期间按下esc按键则退出视频播放，并返回键值
            break;
    } 
```
## 尾巴

waitKey函数是非常简单而且常见的，开始入门的时候需要掌握好它，开发调试的时候waitKey函数同样是一个好帮手。

---

本文由芒果浩明发布，转载需注明来源。
本文链接：https://blog.mangoroom.cn/opencv/opencv-learning-waitKey.html