---
title: "manjaro安装tailscale"
categories: [ "工具" ]
tags: [ "linux","tailscale","manjaro" ]
draft: false
slug: "tools/install-tailscale-on-manjaro"
date: "2021-12-04 22:44:00"
---

本文介绍tailscale客户端在manjaro-linux上的安装，测试在manjaro-kde桌面x86系统平台上正常使用。据tailscale介绍目前arch系linux平台仅支持x86硬件架构平台的系统安装，暂时不支持arm架构平台的安装。

- 1.安装taiscale客户端

```bash
pacman -S tailscale
```

- 2. 使用systemctl启用和启动服务

```bash
sudo systemctl enable --now tailscaled
```

- 3.登录tailscale帐号连接网络

```bash
sudo tailscale up
```
输入该命令后会返回一个登录连接，点击登录连接进行登录帐号操作，成功后会返成功提示。

- 4.查看本机客户端分配所得的ip

```bash
tailscale ip -4
```
或者登录，tailscale后台界面，查看

[https://login.tailscale.com/admin/machines](https://login.tailscale.com/admin/machines)

- 5.其他功能使用

```bash
tailscale -h
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
```


----

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/tools/install-tailscale-on-manjaro.html