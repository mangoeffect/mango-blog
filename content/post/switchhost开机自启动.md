---
title: "switchhost开机自启动"
categories: [ "工具" ]
tags: [ "windows","switchhost","host","auto-start" ]
draft: false
slug: "tools/auto-start-switchhost"
date: "2021-11-15 21:41:00"
---

- 打开windows开机自启动目录

win+r快捷键运行

```
shell:startup
```
![shell-startup.png][1]
回车即可打开windows开机自启动项的目录。一般情况下目录路径应该为

```
C:\Users\UserName\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
#UserName替换为你的用户名
```

- 拷贝SwitchHost快捷启动链接至自启动目录

将桌面的快捷自动方式拷贝至上一步骤打开的快捷启动链接，
![copy-to-startup.png][2]

此时重启电脑即可发现SwitchHost已实现开机自启动

- 管理员权限

完成上一步骤已经可以实现开机自启SwtichHost的目标，但是仍存在一个问题，SwitchHost用于修改系统host的，需要管理员权限。可以通过修改启动快捷方式来获取管理员权限。方法为：右键自启动目录下的快捷方式， 选择安全性，选中你默认的用户名，然后选择编辑，勾选允许修改的权限，最后保存即可。
![pros.png][3]


--------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/tools/auto-start-switchhost.html

  [1]: https://mangoroom.cn/usr/uploads/2021/11/2544566701.png
  [2]: https://mangoroom.cn/usr/uploads/2021/11/2859206002.png
  [3]: https://mangoroom.cn/usr/uploads/2021/11/3727704506.png