---
title: "新路由3OpenWRT安装tailscale"
categories: [ "工具" ]
tags: [ "tailscale","newwifi3","openwrt","immortalwrt" ]
draft: false
slug: "newwifi3-install-tailscale-on-openwrt"
date: "2021-11-12 12:27:00"
---

上一篇文章[tailscale部署私有中继服务器](https://mangoroom.cn/tools/tailscale-custom-derper-servers.html)中介绍了tailscale的自建中继节点的搭建，tailscale在电脑与手机客户端的使用非常简单，下载安装tailscale对应程序登录就可以正常使用了，这里不作介绍。本文介绍tailscale客户端在新路由器3的使用方法。

## 系统固件

新路由3由于性价比很高，玩家很多，适配的第三方系统非常多，芒果用过的就有老毛子、潘多拉、OpenWRT与其分支immortalwrt。这里教程使用的是immortalwrt，为了确保正常使用，推荐使用我自用的固件。下面放出固件下载地址：

[固件下载地址](https://github.com/mangosroom/newifi3-d2-openwrt/actions/runs/1449993369)

上面是我fork别人ibook86大佬项目后修改的，原项目未设置tailscale包的编译，我修改设置开启了编译包含tailscale。下载该页面中24.8m的immortalwrt-ramips-mt7621-d-team_newifi-d2-squashfs-sysupgrade.bin-202111120412固件即可。如果你觉得这个固件所包含的功能还不能满足你的需求，再fork一遍我的项目，修改config_immortalwrt配置文件，重新编译即可。

## 刷机升级

下载固件后需要升级刷机，关于新路由3的刷机升级这里不做过多的图文赘述，下面只说简单过程。

### 方式1

如果你的新路由3现在使用的系统已经是OpenWRT或者OpenWRT的分支系统，那么可以直接到路由器系统后台，找到系统升级备份选项，使用系统的刷机功能是比较方便的.步骤如下：

- 1 登录到路由器系统后台
- 2 找到系统备份升级功能
- 3 上传下载的固件
- 4 **去掉保留配置**，点击刷入固件。
- 5 等待路由器重启，浏览器登录地址为

```
#如果你使用的是本站链接下载的固件
192.168.10.1
password
```

步骤非常简单，其中一个经验是最好去掉保留配置，等刷机完成后重新进去设置比较保险。

### 方式2

如果你的新路由3现在使用的是非OpenWRT版本固件，那么建议你通过Breed不死后台刷入新固件。这一方法需路由器已经事先刷入Breed固件，现在大部分能买的新路由3大概率已经刷入了的，如果没有请自行想办法刷入。下面介绍步骤：

- 1 拔掉路由器电源
- 2 按住路由器reset键不放
- 3 插入电源，等待约几秒钟，路由器指示灯出现全部一起闪烁，放开reset键
- 4 电脑浏览器输入192.168.1.1，进入到Breed界面
- 5 选择固件升级，上传下载的固件，固件类型选择公版即可，点击刷入

等待刷入完毕路由器重启后，即可登录使用。**固件刷入过程中路由器不要断电**。

## 登录tailscale

刷入本站提供的固件是已经包含了tailscale的，但是tailscale的路由器软件包并未提供后台的UI界面的支持，需要在命令行终端中登录和使用它。下面介绍在路由器使用tailscale.

- 1 首先设置好路由器wan口，确保你的路由器已经正常可以连接到公网中使用
- 2 找到系统-终端，打开终端，登录进去，用户名root,密码是你的后台登录密码，如果未修改就是固件开始默认的password

```
ImmortalWrt login: root
Password: 


BusyBox v1.33.1 (2021-11-09 13:25:21 UTC) built-in shell (ash)

┌─────────────────────────────────────────────┐
│                                             │
│ mmmmm                         m       ""#   │
│   #   mmmmm mmmmm  mmm  mmm mm#mm  mmm  #   │
│   #   # # # # # # #" "# #" "  #   "   # #   │
│   #   # # # # # # #   # #     #   m"""# #   │
│ mm#mm # # # # # # "#m#" #     "mm "mm"# "mm │
│                                             │
│─────────────────────────────────────────────│
│              ImmortalWrt 21.02              │
└─────────────────────────────────────────────┘
root@ImmortalWrt:~# 
```
- 3 输入tailscale up, 终端中会返回一个连接
- 4 复制链接到浏览器打开，登录你的tailscale账号，登录成功后，回到终端中会发现有successfully的提示。说明tailscale在路由器的客户端已经登录成功，这时候到你的tailscale设备列表后台就会发现多了一个路由器的设备。

```
https://login.tailscale.com/admin/machines
```
- 5 路由器tailscale连入tailscale组件的局域往后，新路由器就已经属于其中的一台设备，你在外面不用借助frp内网穿透、ddns动态域名解析也可以访问路由器的了。为了确保出门能正常登录路由器后台，这里还需要设置允许互联网登录路由器后台，在后台设置路径为web管理-只允许内网访问，取消只允许内网访问的勾选。设置完毕，你在外面就可以登录家里的新路由3了，登录地址为tailscale设备列表中路由器对应的ip。

- 6 tailscale其他功能使用

在终端中输入tailscale -h 即可查看功能使用命令介绍

```
root@ImmortalWrt:~# tailscale -h
USAGE
  tailscale [flags] <subcommand> [command flags]

For help on subcommands, add --help after: "tailscale status --help".

This CLI is still under active development. Commands and flags will
change in the future.

SUBCOMMANDS
  up         Connect to Tailscale, logging in if needed
  down       Disconnect from Tailscale
  logout     Disconnect from Tailscale and expire current node key
  netcheck   Print an analysis of local network conditions
  ip         Show current Tailscale IP address(es)
  status     Show state of tailscaled and its connections
  ping       Ping a host at the Tailscale layer, see how it routed
  version    Print Tailscale version
  web        Run a web server for controlling Tailscale
  file       Send or receive files
  bugreport  Print a shareable identifier to help diagnose issues

FLAGS
  --socket string
        path to tailscaled's unix socket (default /var/run/tailscale/tailscaled.sock)
```

------------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/tools/newwifi3-install-tailscale-on-openwrt.html

