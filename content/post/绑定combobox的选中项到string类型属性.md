---
title: "绑定combobox的选中项到string类型属性"
categories: [ "DotNet" ]
tags: [ "wpf","combobox","绑定","SelectedItem","string" ]
draft: false
slug: "bind-string-property-to-combobox-selecteditem"
date: "2019-08-21 16:48:00"
---

关于combobox的绑定，网上搜了一遍，绝大多数博文介绍的是绑定combobox到字典类型或者一个拥有id以及名称两个属性的对象。**本文介绍的是将combobox的选中项单独绑定到一个string类型的属性。**

## 绑定的目标

再次说明一下，本文介绍的是将combobox的选中项单独绑定到一个string类型的属性。

例如，下面的情况：

- xaml前端界面

```xml
<ComboBox > 
   <ComboBoxItem>A</ComboBoxItem>
   <ComboBoxItem>B</ComboBoxItem>
   <ComboBoxItem>C</ComboBoxItem>
 </ComboBox>
```
- 后台属性

```c#
public Class MyClass:INotifyPropertyChanged
{
private string myProperty;
public string MyProperty
{
 get{return myProperty;}
 set{
      myProperty=value;
      OnPropertyChanged("MyProperty");
    }
 }
}
```
需要做便是将类中string类型的属性myProperty绑定到combobox，绑定后，选中变化，myProperty属性的值随之改变为选中项的名称。myProperty属性值改变时，combobox界面随之更新显示myProperty的值。

## 如何绑定

下面介绍如何绑定，在介绍绑定之前，先了解combobox的一些属性。在wpf中，combobox拥有三个关于选中的属性以及一个关于显示的属性。它们分别为：

```
// 选中相关
SelectedItem
SelectedValue
SelectedValuePath

// 显示相关
DisplayMemberPath
```
通常情况下，当你绑定SelectedValue属性时，需要指定SelectedValuePath属性。这三个属性都拥有者不同的属性值，当你绑定value时就必须指定value的Path。如在这次的绑定介绍中，如果直接使用
MyProperty绑定SelectedItem或SelectedValue，则会出现的问题是 MyProperty得到的值为System.Windows.Controls.ComboBoxItem:A
之类的。

## 正确的绑定方法如下

```xml
<ComboBox SelectedValue="{Binding Path=MyProperty}"    SelectedValuePath="Content">
  <ComboBoxItem>A</ComboBoxItem>
  <ComboBoxItem>B</ComboBoxItem>
  <ComboBoxItem>C</ComboBoxItem>
</ComboBox>
```
绑定SelectedValue属性并且指定其对应的SelectedValuePath为content（文本内容），如此便可以成功将combobox的选中项单独绑定到一个string类型的属性。

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://mangoroom.cn/donet/bind-string-property-to-combobox-selecteditem.html