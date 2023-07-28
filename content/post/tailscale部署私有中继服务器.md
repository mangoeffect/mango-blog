---
title: "tailscale部署私有中继服务器"
categories: [ "工具" ]
tags: [ "tailscale","frp","derper" ]
draft: false
slug: "tailscale-custom-derper-servers"
date: "2021-09-18 21:38:00"
---

## 更新:自定义端口部署方法

[tailscale部署私有中继服务器-docker部署+自定义端口](https://mangoroom.cn/tools/tailscale-custom-derper-servers-custom-derpport-base-docker.html)

![tailscale](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-bogtailscale-logo.png)
## 安装golang环境

tailscale项目使用的golang环境比较新，部署自定义derper服务要求golang版本1.16以上。推荐尽可能安装最新的版本，本文安装目前最新版本
1.17.1。


### 1. 下载

golang官网在国外，直接从官网下载会比较慢，可以选择国内镜像站下载，如

```bash
wget https://studygolang.com/dl/golang/go1.17.1.linux-amd64.tar.gz
```

注意选择服务器的架构，本文中选择的是amd64架构也就是x86的64位处理器，系统Linux。

### 2. 解压安装

```bash
tar -C /usr/local -xzf go1.17.1.linux-amd64.tar.gz
```

### 3. 设置环境变量

编辑 /etc/profile文件，添加如下内容。编辑文件可以使用vim或者宝塔等其他工具。

```
export GOROOT=/usr/local/go
export GOPATH=/root/go
export GOBIN=$GOPATH/bin
export PATH=$PATH:$GOROOT/bin
export PATH=$PATH:$GOPATH/bin
```
保存退出后，执行以下命令使环境变量生效

```bash
source /etc/profile
```
### 4. 检查golang是否安装成功

```bash
go -version
```
查看golang版本，如果有相应信息返回，则说明golang环境安装成功。

## 安装derper服务

### 1. 下载
安装derper服务，执行以下命令

```
go install tailscale.com/cmd/derper@main
```
等待下载安装完成，查看是否安装成功

### 2. 检查
```
derper -h
```
如果有以下提示信息

```bash
Usage of derper:
  -a string
        server address (default ":443")
  -bootstrap-dns-names string
        optional comma-separated list of hostnames to make available at /bootstrap-dns
  -c string
        config file path
  -certdir string
        directory to store LetsEncrypt certs, if addr's port is :443 (default "/root/.cache/tailscale/derper-certs")
  -dev
        run in localhost development mode
  -hostname string
        LetsEncrypt host name, if addr's port is :443 (default "derp.tailscale.com")
  -logcollection string
        If non-empty, logtail collection to log to
  -mesh-psk-file string
        if non-empty, path to file containing the mesh pre-shared key file. It should contain some hex string; whitespace is trimmed.
  -mesh-with string
        optional comma-separated list of hostnames to mesh with; the server's own hostname can be in the list
  -stun
        also run a STUN server
  -verify-clients
        verify clients to this DERP server through a local tailscaled instance.
```
则说明derper安装成功

### 3. 启动服务

启动derper服务，执行以下命令即可。在此之前你需要完成以下准备：

- 拥有一个域名，解析到服务器ip(国内则需要先备案)
- 服务器安全组和系统防火墙放开443端口、3478端口

推荐腾讯云，新用户优惠力度还是比较大的，在带宽上感觉也比阿里云要慷慨一些。优惠链接

[![【腾讯云】云产品限时秒杀，爆款1核2G云服务器，首年74元](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-bogad-tencentcloud.jpg)](https://mangoroom.cn/go/tencentcloud1/)

```
derper -c=$HOME/derper.conf -hostname=yourhostname.com -stun
```
当看到以下返回信息，则说明derper服务成功了

```
2021/09/18 15:04:31 derper: serving on :443 with TLS
2021/09/18 15:04:31 running STUN server on [::]:3478
```
443端口比较宝贵，芒果测试了使用其他端口，但只能链接上节点，无法进行stun服务器的中转，暂时还找不到解决办法，后续解决后再更新。

### 4. derper进程守护

我们希望derper服务一直在线，则需要用进程守护工具。这里推荐使用宝塔的应用管理器插件。
配置如下：

```
名称:derper
执行文件：derper
命令参数：-c=/root/derper.conf -hostname=yourhostname.com -stun
```

## 配置控制台节点信息

```
 "ACLs": [
    // Match absolutely everything. Comment out this section if you want
    // to define specific ACL restrictions.
    { "Action": "accept", "Users": ["*"], "Ports": ["*:*"] },
  ],
  "derpMap": {
    "Regions": { "900": {
      "RegionID": 900,
      "RegionCode": "myderp",
      "Nodes": [{
          "Name": "1",
          "RegionID": 900,
          "HostName":"yourhostname.com",
      }]
    }}
  }
```

## 重启客户端服务

以windows为例，重启服务可以打开任务管理器-服务，找到tailscale服务，右键选择重启启动即可。
![restart-tailscale](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-bogrestart-tailscale-on-windows.png)

重启后，如果一切按步骤没问题，在控制台的设备详细页面就会看到已经链接上自建的节点。
![tailscale-relays](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-bogtailscale-relays.png)
可以看到延迟相比官方的国外节点低很多。

## 节点分享

自建的中继节点服务器是可以分享给他人使用的，芒果这里就将自建的节点分享出来，以方便没有条件或者不方便自建中继服务器的朋友，在控制主页填入一下配置信息即可

```
// Example/default ACLs for unrestricted connections.
{
  // Declare static groups of users beyond those in the identity service.
  "Groups": {
    "group:example": [ "user1@example.com", "user2@example.com" ],
  },
  // Declare convenient hostname aliases to use in place of IP addresses.
  "Hosts": {
     "example-host-1": "100.100.100.100",
  },
  "ACLs": [
    // Match absolutely everything. Comment out this section if you want
    // to define specific ACL restrictions.
    { "Action": "accept", "Users": ["*"], "Ports": ["*:*"] },
  ],
  "derpMap": {
    "Regions": { "900": {
      "RegionID": 900,
      "RegionCode": "mangoderp",
      "Nodes": [{
          "Name": "1",
          "RegionID": 900,
          //本节点由mangoroom.cn提供，请勿滥用。详情访问https://mangoroom.cn/tools/tailscale-custom-derper-servers.html
          "HostName":"tailscalederper.mangoroom.cn",
          "DERPPort":8082
      }]
    }}
  }
}
```
**节点为腾讯云广州节点，带宽有限，请勿滥用，如果想支持本节点可双击下方点赞或者请芒果喝一杯奶茶**

## 多节点配置

- 写法一

```
  "Nodes": [{
          "Name": "1",
          "RegionID": 900,
          "HostName":"node1.com",
      },
      {
          "Name": "2",
          "RegionID": 900,
          "HostName":"node2.com",
      }]
```

- 写法二

```
{
"ACLs":
"Action":"accept","Users":["∗"],"Ports":["∗:∗"]
,
"derpMap": {
"OmitDefaultRegions": false,
"Regions": { "900": {
"RegionID": 900,
"RegionCode": "HK",
"Nodes":
"Name":"HKPROXY","RegionID":900,"IPv4":"x.x.x.x","DERPPort":666//自定义端口

},"901": {
"RegionID": 901,
"RegionCode": "EU",
"Nodes":
"Name":"FARM","RegionID":900,"IPv4":"x.x.x.x","DERPPort":666//自定义端口

}

}
}
}
```

-----------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/tools/tailscale-custom-derper-servers.html

