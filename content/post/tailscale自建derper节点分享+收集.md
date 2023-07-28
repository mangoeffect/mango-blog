---
title: "tailscale自建derper节点分享+收集"
categories: [ "工具" ]
tags: [ "tailscale" ]
draft: false
slug: "tailscale-custom-derper-relays-share-and-collection"
date: "2022-03-09 11:21:53"
---

本页面旨在收集和分享国内的自建tailscale节点，如果你愿意分享自己的derper节点，可在下方评论区分享后我加入配置列表

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