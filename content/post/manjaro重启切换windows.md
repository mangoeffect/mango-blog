---
title: "manjaro重启切换windows"
categories: [ "工具" ]
tags: [ "windows","manjaro","reboot","grub" ]
draft: false
slug: "reboot-to-windows-by-cammand-line-on-manjaro"
date: "2021-12-09 20:20:21"
---

![manjaro&&windows](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-blog-imagesmanjaro&&windows.jpg)
远程到manjaro+windows的双系统电脑时，从manjaro切换至windows只能通过命令行重启，并基于grub启动引导实现重启切换至windows的操作。

## 步骤

### 1. 编辑grub配置文件

Edit the /etc/default/grub and replace GRUB_DEFAULT=0 with GRUB_DEFAULT=saved

### 2. 更新grub设置

```bash
sudo update-grub
```

### 3. 执行以下命令重启

```bash
sudo grub-reboot "$(grep -i windows /boot/grub/grub.cfg|cut -d"'" -f2)" && sudo reboot
```