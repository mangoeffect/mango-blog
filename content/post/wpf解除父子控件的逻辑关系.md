---
title: "wpf解除父子控件的逻辑关系"
categories: [ "DotNet" ]
tags: [ "c#","wpf" ]
draft: false
slug: "DotNet/unteases-logical-relationship-between-parent-child-controls-in-wpf"
date: "2019-12-13 10:26:00"
---

在将一个元素的子控件add到新控件时遇到了如下的错误：

**WPF错误：指定的元素已经是另一个元素的逻辑子元素。请先将其断开连接。**

## 场景

在原窗口新增一个tab页，原窗口的控件与元素不变，新tab窗口部分的元素与原窗口一致，直接将原窗口的控件与元素add到新tab页会爆出此错误。

## 原因

发生此错误的原因是，xml文档是一个树形结构的，一个对象有且仅有一个父节点，即xml中的任何一个对象元素只能被包含在一个节点之下。xaml为xml的拓展，同样遵循这种规范，xaml文档里的节点对象对应着窗口中的控件元素，所以当一个元素已经被包含在某个元素之下时，将其包含在新的元素中就会发生此错误。

## 解决办法

已经知道了问题的关键原因在于一个元素只能拥有一个父节点，解决办法就是解除控件的父子关系，之后再重新加入的新的父控件之下。解除关系方法如下

- 如果element是Panel（例如Grid）的孩子，则必须将其从Panel的Children集合中删除。

```
先创建临时变量将子元素存起来，再从父控件中将子控件remove。
```

- 如果它设置为ContentControl的Content，则必须将该内容设置为空（或其他任何不是element）。

```c#
    UserControl control = obj1 as UserControl;
    Grid riid = control.Content as Grid;
    DependencyObject parent = riid.Parent;
    if (parent != null)
    {
        parent.SetValue(ContentPresenter.ContentProperty, null);
    }
```
---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/donet/unteases-logical-relationship-between-parent-child-controls-in-wpf.html
