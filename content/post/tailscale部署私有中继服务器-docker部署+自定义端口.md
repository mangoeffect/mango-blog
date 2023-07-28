---
title: "tailscale部署私有中继服务器-docker部署+自定义端口"
categories: [ "工具" ]
tags: [ "tailscale","derper","docker","derpport" ]
draft: false
slug: "tailscale-custom-derper-servers-custom-derpport-base-docker"
date: "2022-03-05 11:17:00"
---

本文在以下环境安装测试通过

- 腾讯云轻量应用服务器ubuntu-20.04 lts
- Let's Encrypt证书
- zouyq/derper镜像
- (部署在国内服务器域名需备案)

[![【腾讯云】云产品限时秒杀，爆款1核2G云服务器，首年74元](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-bogad-tencentcloud.jpg)](https://mangoroom.cn/go/tencentcloud1/)

## 安装docker

ssh远程登陆到服务器,推荐使用一键安装命令

```
curl -sSL https://get.daocloud.io/docker | sh
```

## 申请证书

芒果这里是申请免费的Let's Encrypt证书，理论上使用其它的也是ok的。Let's Encrypt证书的申请方法一可以自己到官方网站去申请，而是可以用宝塔面板自建一个静态页面网站，使用宝塔工具申请。芒果这里是使用宝塔申请。申请完毕后得到一个证书文件与密钥文件

```
# 使用宝塔申请的证书文件一般存在/www/server/panel/vhost/certz中
fullchain.pem #证书文件
privkey.pem #密钥文件
```
然后将其拷贝只根目录下

```
certpath
```
目录中,并且按照以下格式重命名证书和密钥文件

```
yourhostname.crt
yourhostname.key
```

## 安装镜像

ssh远程登陆到服务器

```bash
sudo docker pull zouyq/derper
sudo docker run -it -d -p 8082:8082 -p 3478:3478/udp --name derper  -v /certpath:/cert zouyq/derper /derper -hostname your-hostname -stun -a :8082 -certmode manual -certdir /cert 
# 其中your-hostname 替换为你的域名
```

### 检查启动状态

```bash
sudo docker logs 036401e7240b
# 命令格式为sudo docker logs <容器id>, 可以使用sudo docker ps -a查看
```
如果有以下提示，无报错则说明deper服务已经正常启动

```
2022/03/05 02:23:04 no config path specified; using /var/lib/derper/derper.key
2022/03/05 02:23:04 derper: serving on :8082 with TLS
2022/03/05 02:23:04 running STUN server on [::]:3478
```

### 防火墙放行

- 在服务器运行商控制台面板安全组放行8082(tcp), 3478(udp)
- 在linux系统防火墙放行8082(tcp), 3478(udp)，可以使用宝塔面板设置

## 修改tailscale配置

tailscale的节点设置与之前类似，但使用了非443端口，需要在配置里面指定说明,如

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

## 测试

部署完后可以进行以下测试，检验是否可以正常使用

- 1 将tailscale改为仅链接自定义节点，如

```
 "derpMap": {
    "OmitDefaultRegions": true,
    "Regions": { "900": {
      "RegionID": 900,
      "RegionCode": "myderp",
      "Nodes": [{
          "Name": "1",
          "RegionID": 900,
          "HostName":"tailscalederper.mangoroom.cn",
          "DERPPort":8082
      }]
    }}
  }
```
- 2 重启各个客户端
- 3 各个客户之间相互ping或者发送文件

如果以上测试没有问题，那么整个部署就OK了。

----------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/tools/tailscale-custom-derper-servers-custom-derpport-base-docker.html