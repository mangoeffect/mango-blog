---
title: "PCL直通滤波"
categories: [ "算法","PCL" ]
tags: [ "滤波","pcl","直通滤波" ]
draft: false
slug: "algorithm/passthrough-filter-of-pcl"
date: "2020-09-28 18:10:00"
---

PCL库实现了多种点云的滤波方法，直通滤波（passthrough filter)为众多滤波方法里面比较基础简单的滤波方法。直通滤波原理非常简单，顾名思义也能猜出其滤波的方法流程，"直通"即基于点云空间坐标系设定一个通道，将点云在通道范围之外的点剔除滤掉从而保留通道里边的点云。这个"通道"在PCL里面具体表现为针对某个坐标轴设定的限定范围，比如：

```cpp
setFilterFieldName("z");
setFilterLimits(0.0f, 1.0f);
```
以上滤波只保留点云z轴范围在[0.0, 1.0]之内的点云

而

```cpp
setFilterFieldName("x");
setFilterLimits(-2.0f, 3.0f);
```
则滤波只保留点云x轴范围在[-2.0, 3.0]之内的点云。其原理非常简单，下面直接贴出使用示例代码

```cpp
#include <iostream>
#include <random>

#include "pcl/point_types.h"
#include "pcl/filters/passthrough.h"
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

    for(int i = 0; i < 3000; ++i)
    {
        float x =  dis(random);
        float y =  dis(random);
        float z =  dis(random);
        cloud->points.push_back({x, y, z});
    }
    
    //step2: 创建直通滤波对象
    pcl::PassThrough<pcl::PointXYZ> pass_through_filter;

    //step3: 设置直通滤波参数
    pass_through_filter.setInputCloud(cloud);
    pass_through_filter.setFilterFieldName("z");
    pass_through_filter.setFilterLimits(0.0f, 1.0f);

    //step4: 执行滤波处理
    pass_through_filter.filter(*cloud_filtered);

    //step5: 可视化滤波结果

	pass_through_filter.setFilterLimits(1.0f, 3.0f);//重新滤波，提取第一次滤波剔除的部分点云
	pass_through_filter.filter(*cloud_removed);

	pcl::visualization::PCLVisualizer viewer("pcl pass throuhg filter");
	pcl::visualization::PointCloudColorHandlerCustom<pcl::PointXYZ> cloud_handler(cloud_removed, 255, 0, 0);
	viewer.addPointCloud(cloud_removed, cloud_handler, "cloud_removed");

	pcl::visualization::PointCloudColorHandlerCustom<pcl::PointXYZ> filtered_handler(cloud_filtered , 0, 255, 0);
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
执行程序的结果如图

![pcl-passthrough-filter.png][1]
坐标轴xyz顺序是rgb颜色排序的，从蓝色z轴划分，绿色为第一遍[0,1]的滤波结果，红色则是为[1, 3]滤波结果


若是想要保留通道之外的点云，不想要限定坐标轴之内的点云，PCL提供了一整个设置取反的参数，只需要在滤波之前添加一行代码即可

```cpp
pass_through_filter.setFilterLimitsNegative(false);
```

取反测试示例完整代码

```cpp
#include <iostream>
#include <random>

#include "pcl/point_types.h"
#include "pcl/filters/passthrough.h"
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

    for(int i = 0; i < 3000; ++i)
    {
        float x =  dis(random);
        float y =  dis(random);
        float z =  dis(random);
        cloud->points.push_back({x, y, z});
    }
    
    //step2: 创建直通滤波对象
    pcl::PassThrough<pcl::PointXYZ> pass_through_filter;

    //step3: 设置直通滤波参数
    pass_through_filter.setInputCloud(cloud);
	//设置取反标志
	pass_through_filter.setFilterLimitsNegative(true);

    pass_through_filter.setFilterFieldName("z");
    pass_through_filter.setFilterLimits(0.0f, 1.0f);

    //step4: 执行滤波处理
    pass_through_filter.filter(*cloud_filtered);

    //step5: 可视化滤波结果

	pass_through_filter.setFilterLimits(1.0f, 3.0f);//重新滤波，提取第一次滤波剔除的部分点云
	pass_through_filter.filter(*cloud_removed);


	pcl::visualization::PCLVisualizer viewer("pcl pass throuhg filter");
	pcl::visualization::PointCloudColorHandlerCustom<pcl::PointXYZ> cloud_handler(cloud_removed, 255, 0, 0);
	viewer.addPointCloud(cloud_removed, cloud_handler, "cloud_removed");

	pcl::visualization::PointCloudColorHandlerCustom<pcl::PointXYZ> filtered_handler(cloud_filtered , 0, 255, 0);
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

执行代码结果如下

![pcl-passthrough-filter-negative.png][2]
可以看到与先前滤波相比，红蓝点云位置置反了。

---------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/algorithm/passthrough-filter-of-pcl.html

  [1]: https://mangoroom.cn/usr/uploads/2020/09/1501688772.png
  [2]: https://mangoroom.cn/usr/uploads/2020/09/3134548068.png