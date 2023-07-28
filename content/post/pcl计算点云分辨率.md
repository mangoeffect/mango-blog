---
title: "pcl计算点云分辨率"
categories: [ "PCL" ]
tags: [ "pcl","resolution" ]
draft: false
slug: "how-to-compute-resolution-of-cloud"
date: "2020-10-09 20:22:00"
---

## 空间分辨率

引用百度百科的解释

> 空间分辨率，是指遥感图像上能够详细区分的最小单元的尺寸或大小，是用来表征影像分辨地面目标细节的指标。通常用像元大小、像解率或视场角来表示。
空间分辨率是评价传感器性能和遥感信息的重要指标之一，也是识别地物形状大小的重要依据。

但是在三维空间上是没有二维图像这般像元量化的，所以也无法得到实际的上的“像元”，而分辨率是体现图像的细节细腻程度，放到点云上能体现物体细节细腻程度的只有点云的密集程度了，而这个密集程度可以用两点之间间隔距离来表示。接下来要求的便是点云中平均两点之间的距离为。

## 计算方法

计算方法非常简单，步骤如下

-  step1: 遍历点云中的每一个点
-  step2: 找到与其最近的点，并求出两点距离
-  step3: 累加两点距离之和，并除以点云数量（剔除无效点），所得结果便是点云空间分辨率

## 实现代码

代码非常简单，完整的实现代码如下

```cpp
double MatchModel::ComputeCloudResolution(const pcl::PointCloud<pcl::PointXYZ>::ConstPtr& cloud)
{
    double res = 0.0;
    int n_points = 0;
    int nres;
    std::vector<int> indices(2);
    std::vector<float> sqr_distances(2);
    //step1: 新建kdtree用于搜索
    pcl::search::KdTree<PointXYZ> tree;
    tree.setInputCloud(cloud);
    
    //step2: 遍历点云每个点，并找出与它距离最近的点
    for (size_t i = 0; i < cloud->size(); ++i)
    {
        if (!pcl_isfinite((*cloud)[i].x))
        {
            continue;
        }
        // 取第二个距离，因为第一个是它本身
        nres = tree.nearestKSearch(i, 2, indices, sqr_distances);
        //step3: 统计最小距离和、有效点数量
        if (nres == 2)
        {
            res += sqrt(sqr_distances[1]);
            ++n_points;
        }
    }

    //step4: 计算空间分辨率
    if (n_points != 0)
    {
        res /= n_points;
    }
    return res;
}
```

------------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/pcl/how-to-compute-resolution-of-cloud.html