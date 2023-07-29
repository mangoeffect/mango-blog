---
title: "PCL条件滤波"
categories: [ "算法","PCL" ]
tags: [ "滤波","pcl" ]
draft: false
slug: "algorithm/condition-removal-filter-of-pcl"
date: "2020-09-29 19:10:00"
---

## 简介

本篇笔记记录了PCL库条件滤波的使用。在上一篇笔记[PCL直通滤波](https://mangoroom.cn/algorithm/passthrough-filter-of-pcl.html)中介绍了PCL的直通滤波方法。直通滤波方法比较简单粗暴，直接“一刀切”的思想对某个坐标轴范围剔除。这在简单的场景会比较简单有效，但一旦涉及稍稍复杂一点点的点云剔除直通滤波用起来就没那么方便顺手了，因为直通滤波一次只能对某个坐标轴的范围进行滤波，假如想限制两个轴以上的范围直通滤波就无能为力了。

而条件滤波（condition removal filter)正好可以解决这个问题，条件滤波相比于直通滤波优点就是一次可以对多个轴的范围进行划定限制。允许自由地添加和组合xyz三个坐标轴方向的范围限制，相比与直通滤波的“一刀切”剔除点云的方法，条件滤波则是你想怎么切就怎么切，切几刀都行。所以可以把条件滤波看作一个加强版的，可自定义的直通滤波看待。下面直接放出使用示例代码：

## 代码

```cpp
#include <iostream>
#include <random>

#include "pcl/point_types.h"
#include "pcl/filters/conditional_removal.h"
#include "pcl/visualization/pcl_visualizer.h"

int main(int argc, char** argv)
{
	pcl::PointCloud<pcl::PointXYZ>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZ>);
	pcl::PointCloud<pcl::PointXYZ>::Ptr cloud_filtered(new pcl::PointCloud<pcl::PointXYZ>);
	pcl::PointCloud<pcl::PointXYZ>::Ptr cloud_removed(new pcl::PointCloud<pcl::PointXYZ>);

	//step1: 创建点云数据
	std::random_device rd;
	std::default_random_engine random(rd());
	std::uniform_real_distribution<float> dis(0.0f, 3.0f);

	for (int i = 0; i < 3000; ++i)
	{
		float x = dis(random);
		float y = dis(random);
		float z = dis(random);
		cloud->points.push_back({ x, y, z });
	}

	//step2: 创建条件滤波对象
	pcl::ConditionalRemoval<pcl::PointXYZ> conditional_removal;

	//step3: 设置条件滤波参数

	//条件与
	//保留点云xyz坐标都在[1,2]之内的点
	pcl::ConditionAnd<pcl::PointXYZ>::Ptr condition_add(new pcl::ConditionAnd<pcl::PointXYZ>());
	condition_add->addComparison(pcl::FieldComparison<pcl::PointXYZ>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZ>("z", pcl::ComparisonOps::GE, 1.0)));
	condition_add->addComparison(pcl::FieldComparison<pcl::PointXYZ>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZ>("z", pcl::ComparisonOps::LE, 2.0)));
	condition_add->addComparison(pcl::FieldComparison<pcl::PointXYZ>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZ>("y", pcl::ComparisonOps::GE, 1.0)));
	condition_add->addComparison(pcl::FieldComparison<pcl::PointXYZ>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZ>("y", pcl::ComparisonOps::LE, 2.0)));
	condition_add->addComparison(pcl::FieldComparison<pcl::PointXYZ>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZ>("x", pcl::ComparisonOps::GE, 1.0)));
	condition_add->addComparison(pcl::FieldComparison<pcl::PointXYZ>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZ>("x", pcl::ComparisonOps::LE, 2.0)));

	//条件或
	pcl::ConditionOr<pcl::PointXYZ>::Ptr condition_or(new pcl::ConditionOr<pcl::PointXYZ>());
	condition_or->addComparison(pcl::FieldComparison<pcl::PointXYZ>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZ>("z", pcl::ComparisonOps::GE, 2)));
	condition_or->addComparison(pcl::FieldComparison<pcl::PointXYZ>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZ>("x", pcl::ComparisonOps::GE, 1.5)));

	//condition_add->addCondition(condition_or);
	condition_or->addCondition(condition_add);
	 
	conditional_removal.setCondition(condition_or);
	conditional_removal.setInputCloud(cloud);


	//step4: 执行滤波处理
	conditional_removal.filter(*cloud_filtered);

	//step5: 可视化滤波结果
	pcl::visualization::PCLVisualizer viewer("pcl condition removal filter");
	pcl::visualization::PointCloudColorHandlerCustom<pcl::PointXYZ> cloud_handler(cloud, 255, 0, 0);
	viewer.addPointCloud(cloud, cloud_handler, "cloud");

	pcl::visualization::PointCloudColorHandlerCustom<pcl::PointXYZ> filtered_handler(cloud_filtered, 0, 255, 0);
	viewer.addPointCloud(cloud_filtered, filtered_handler, "cloud_filtered");

	viewer.addCoordinateSystem(1);

	while (!viewer.wasStopped())
	{
		viewer.spinOnce();
	}
	viewer.close();

	return 0;
}
```

## 说明

流程步骤在代码中注释已经简单说明了，这里就几个点做一下补充。

- api调用流程为：创建滤波对象，创建条件对象（可以是and型或者or型，或者合并两者），设置条件，滤波
- 条件对象添加条件的格式为

```cpp
condition_add->addComparison(pcl::FieldComparison<pcl::PointXYZ>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZ>("z", pcl::ComparisonOps::GE, 1.0)));
```
参数z表示设置z轴的范围条件，GE,1.0是 greater equal， 即表示z轴要大于等于1.0。其他类似还有

```cpp
pcl::ComparisonOps::LE //小于等于
pcl::ComparisonOps::GT //大于
pcl::ComparisonOps::LT //小于
pcl::ComparisonOps::EQ //相等
```

- 设置条件

setCondition可以值用ConditionAnd或者ConditionOr条件对象。

只用ConditionAnd测试

```cpp
conditional_removal.setCondition(condition_add);
```
![pcl-condition-removal-filter.png][1]

只用ConditionOr测试

```cpp
conditional_removal.setCondition(condition_or);
```

![condition_or_merge_condition_add.png][2]
也可以将ConditionAnd合并到ConditionOr后设置ConditionOr，反过来也可以将ConditionOr合并到ConditionAnd后设置ConditionAnd，但需要注意的是添加的顺序不一样，组合成的条件结果是不一样的，这个与编程里边&&、||逻辑与或的短路原理是一样的。

ConditionAnd合并到ConditionOr后设置ConditionOr

```cpp
condition_or->addCondition(condition_add);
conditional_removal.setCondition(condition_or);
```
![condition_or_merge_condition_add.png][3]
此时合并遵循``||``或的逻辑，所以效果与单独设置ConditionOr是一样的

ConditionOr合并到ConditionAnd后设置ConditionAnd

```cpp
condition_add->addCondition(condition_or);
conditional_removal.setCondition(condition_add);
```
![condition_add_merge_condition_or.png][4]

此时合并遵循``&&``与的逻辑，所以效果与单独设置ConditionAnd是一样的

------------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/algorithm/condition-removal-filter-of-pcl.html

  [1]: https://mangoroom.cn/usr/uploads/2020/09/2682866710.png
  [2]: https://mangoroom.cn/usr/uploads/2020/09/2208493697.png
  [3]: https://mangoroom.cn/usr/uploads/2020/09/2208493697.png
  [4]: https://mangoroom.cn/usr/uploads/2020/09/105470233.png