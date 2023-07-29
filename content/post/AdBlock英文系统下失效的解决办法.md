---
title: "AdBlock英文系统下失效的解决办法"
categories: [ "工具" ]
tags: [ "工具使用" ]
draft: false
slug: "tools/adblock-not-work-in-english-system"
date: "2019-06-18 10:36:00"
---

在英文系统的环境下，chrome浏览器安装Adblock插件会出现失效的情况，笔者在Ubuntu和Windows两个环境下均出现如此的情况。经一番搜索查询，出现问题的原因是

> 插件的设置里面有一个Filter Lists，即广告过滤的列变（规则），里面包含着不同语言地区的不同过滤列表，英文系统的chrome浏览器安装AdBlock插件后不会包含中文的过滤列表，所以出现访问网页的时候网站广告屏蔽失效，解决办法是手动添加中文过滤列表Ad Blocking Filter Lists

步骤如下

1. Options进入设置
   ![adblock-1.png][1]
2. Filter Lists ->Ad Blocking Filter Lists ->Add filters for another language->Chinese + EasyList

   ![adblock-2.png][2]

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/tools/adblock-not-work-in-english-system.html

  [1]: https://mangoroom.cn/usr/uploads/2019/06/3401282579.png
  [2]: https://mangoroom.cn/usr/uploads/2019/06/500016610.png