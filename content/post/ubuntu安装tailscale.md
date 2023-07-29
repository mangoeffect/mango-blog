---
title: "ubuntu安装tailscale"
categories: [ "工具" ]
tags: [ "linux","tailscale","ubuntu" ]
draft: false
slug: "tools/install-tailscale-on-ubuntu"
date: "2021-12-04 18:39:00"
---

本文介绍tailscale客户端在ubuntu linux上面的安装。测试在ubuntu 20.04lts的x86平台正常使用，理论上x86和arm平台都适用。

- 1.添加 Tailscale 的包签名密钥和存储库

```bash
curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/focal.gpg | sudo apt-key add -
curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/focal.list | sudo tee /etc/apt/sources.list.d/tailscale.list
```

- 2.安装tailscae

```bash
sudo apt-get update
sudo apt-get install tailscale
```

- 3.登录tailscale并连接到网络

```bash
sudo tailscale up
```
执行命令行，会返回一个登录的连接入口，ctr+点击进入浏览器登录界面，后续步骤与windows客户端一样，登录自己的tailscale帐号，登录成功后终端会返回成功的提示。

- 4.查看本机分配的tailscale ip 

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
本文链接：https://blog.mangoroom.cn/tools/install-tailscale-on-ubuntu.html

