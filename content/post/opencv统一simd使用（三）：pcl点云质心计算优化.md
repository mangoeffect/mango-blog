---
title: "opencv统一simd使用（三）：pcl点云质心计算优化"
categories: [ "opencv","c++","计算机视觉","PCL" ]
tags: [ "opencv","pcl","openmp","simd","centroid" ]
draft: false
slug: "pcl-compute-centroid-optimization-benchmark"
date: "2021-10-24 23:15:00"
---

## 计算质心

计算质心的原理很简单，即将所有坐标点相加求和取平均值，不做优化的程序一个循环即可

```cpp
//计算质心原始程序
void GetCentroid(pcl::PointCloud<pcl::PointXYZ>::ConstPtr cloud, pcl::PointXYZ& centroid)
{
    if (cloud->empty()) { return; }

    int count = cloud->size();

    float x_sum = 0;
    float y_sum = 0;
    float z_sum = 0;
    for (int i = 0; i < count; i++)
    {
        x_sum += cloud->points[i].x;
        y_sum += cloud->points[i].y;
        z_sum += cloud->points[i].z;
    }
    centroid.x = x_sum /count;
    centroid.y = y_sum /count;
    centroid.z = z_sum /count;
}
```
## OpenCV统一Simd框架优化

Simd优化也非常简单，需要注意的就是使用v_load_deinterleave加载坐标点的xyz部分时，需要定义一个占位数据，因为pcl中一个Point数据大小为4个float,其中一个作为保留字段给其他类型Point使用。

```cpp
void GetCentroidSimd(pcl::PointCloud<pcl::PointXYZ>::ConstPtr cloud, pcl::PointXYZ& centroid)
{
    if(cloud->empty()) {return;}

    int count = cloud->size();
    cv::v_float32 centroidx = cv::vx_setzero_f32();
    cv::v_float32 centroidy = cv::vx_setzero_f32();
    cv::v_float32 centroidz = cv::vx_setzero_f32();
    int step = cv::v_float32::nlanes;

    for (int i = 0; i < count; i += step)
    {
          //固定长方法
          //cv::v_float32 vx(cloud->points[i].x, cloud->points[i + 1].x, cloud->points[i + 2].x, cloud->points[i + 3].x);
          //cv::v_float32 vy(cloud->points[i].y, cloud->points[i + 1].y, cloud->points[i + 2].y, cloud->points[i + 3].y);
          //cv::v_float32 vz(cloud->points[i].z, cloud->points[i + 1].z, cloud->points[i + 2].z, cloud->points[i + 3].z);
          cv::v_float32 vx;
          cv::v_float32 vy;
          cv::v_float32 vz;
          cv::v_float32 vs;//占位数据
          cv::v_load_deinterleave(&cloud->points[i].x, vx, vy, vz, vs);

          centroidx += vx;
          centroidy += vy;
          centroidz += vz;
    }
    centroid.x = cv::v_reduce_sum(centroidx);
    centroid.y = cv::v_reduce_sum(centroidy);
    centroid.z = cv::v_reduce_sum(centroidz);
    for (size_t i = count - count % step; i < count; i++)
    {
        centroid.x += cloud->points[i].x;
        centroid.y += cloud->points[i].y;
        centroid.z += cloud->points[i].z;
    }
    centroid.x /= count;
    centroid.y /= count;
    centroid.z /= count;
}
```

## OpenMP并行优化

单循环的OpenMP并行优化也非常简单，需要注意的时xyz数据是具有累加的，此时需要进行reduction操作，需要定义好参与reduction的变量。

```cpp
void GetCentroidOpenMP(pcl::PointCloud<pcl::PointXYZ>::ConstPtr cloud, pcl::PointXYZ& centroid)
{
    if (cloud->empty()) { return; }

    int count = cloud->size();

    float x_sum = 0;
    float y_sum = 0;
    float z_sum = 0;
#pragma omp parallel for reduction(+:x_sum,y_sum,z_sum)
    for (int i = 0; i < count; i++)
    {
        x_sum += cloud->points[i].x;
        y_sum += cloud->points[i].y;
        z_sum += cloud->points[i].z;
    }
    centroid.x = x_sum /count;
    centroid.y = y_sum /count;
    centroid.z = z_sum /count;
}
```

## Simd加上OpenMP并行优化

结合Simd与OpenMP的优化则相对复杂一点，而且在有reduction操作时的并行，同时加上Simd优化，效率可能不升反降。这不知道是否与手动处理reduction有关系，后续的测试表明下面的优化程序反而大幅度降低了效率，耗时反而比不做优化的程序高。

```cpp

void GetCentroidSimdWithOpenMP(pcl::PointCloud<pcl::PointXYZ>::ConstPtr cloud, pcl::PointXYZ& centroid)
{
    if (cloud->empty()) { return; }

    int count = cloud->size();
    cv::v_float32x4 centroidx = cv::v_setzero_f32();
    cv::v_float32x4 centroidy = cv::v_setzero_f32();
    cv::v_float32x4 centroidz = cv::v_setzero_f32();
    int step = cv::v_float32x4::nlanes;

    //#pragma omp parallel for reduction(+:centroidx,centroidy,centroidz) : 错误，v_float32类型不可做reduction，下面手动reduction
    //std::vector<cv::v_float32x4> centroidx_arr(4, cv::v_setzero_f32());
    //std::vector<cv::v_float32x4> centroidy_arr(4, cv::v_setzero_f32());
    //std::vector<cv::v_float32x4> centroidz_arr(4, cv::v_setzero_f32());

    std::array<cv::v_float32x4, 4> centroidx_arr;
    std::array<cv::v_float32x4, 4> centroidy_arr;
    std::array<cv::v_float32x4, 4> centroidz_arr;
#pragma omp parallel for
    for (int i = 0; i < count; i += step)
    {
        cv::v_float32x4 vx;
        cv::v_float32x4 vy;
        cv::v_float32x4 vz;
        cv::v_float32x4 vs;//占位数据
        cv::v_load_deinterleave(&cloud->points[i].x, vx, vy, vz, vs);
        
        int id = omp_get_thread_num();
        centroidx_arr[id] += vx;
        centroidy_arr[id] += vy;
        centroidz_arr[id] += vz;
    }
    //手动reduction
    for (auto& a : centroidx_arr){ centroidx += a; }
    for (auto& a : centroidy_arr){ centroidy += a; }
    for (auto& a : centroidz_arr){ centroidz += a; }
   
    centroid.x = cv::v_reduce_sum(centroidx);
    centroid.y = cv::v_reduce_sum(centroidy);
    centroid.z = cv::v_reduce_sum(centroidz);
    for (size_t i = count - count % step; i < count; i++)
    {
        centroid.x += cloud->points[i].x;
        centroid.y += cloud->points[i].y;
        centroid.z += cloud->points[i].z;
    }
    centroid.x /= count;
    centroid.y /= count;
    centroid.z /= count;
}
```

## 测试对比

下面对pcl原始计算质心程序，simd优化程序，openmp优化程序以及simd&openmp优化做测试对比，对比方法为测试随机生成的点云，测试10次取耗时均值，并不断增加点云规模查看耗时变化趋势。

```cpp

int main(int argc, char** argv)
{
    pcl::PointCloud<pcl::PointXYZ>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZ>);
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_real_distribution<float> dist(-1000.0f, 1000.0f);

    std::vector<double> x;
    std::vector<double> none_y;
    std::vector<double> simd_y;
    std::vector<double> openmp_y;
    std::vector<double> simd_openmp_y;

    for (size_t p = 1; p < 8; p++)
    {
        size_t point_size = std::pow(10, p);
        double none_t = 0;
        double simd_t = 0;
        double openmp_t = 0;
        double simd_openmp_t = 0;
        size_t test_count = 10;
        for (size_t k = 0; k < test_count; k++)
        {
            cloud->clear();
            for (size_t i = 0; i < point_size; i++)
            {
                cloud->push_back(pcl::PointXYZ(dist(gen), dist(gen), dist(gen)));
            }

            pcl::PointXYZ centroid;
            auto t0 = std::chrono::system_clock::now();
            pcl::computeCentroid(*cloud, centroid);
            auto t1 = std::chrono::system_clock::now();
            GetCentroidSimd(cloud, centroid);
            auto t2 = std::chrono::system_clock::now();
            GetCentroidOpenMP(cloud, centroid);
            auto t3 = std::chrono::system_clock::now();
            GetCentroidSimdWithOpenMP(cloud, centroid);
            auto t4 = std::chrono::system_clock::now();
            none_t += std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0).count();
            simd_t += std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count();
            openmp_t += std::chrono::duration_cast<std::chrono::milliseconds>(t3 - t2).count();
            simd_openmp_t += std::chrono::duration_cast<std::chrono::milliseconds>(t4 - t3).count();
        }

        std::cout << "none_t = " << none_t / test_count << " ms." << std::endl;
        std::cout << "simd_t = " << simd_t / test_count << " ms." << std::endl;
        std::cout << "openmp_t = " << openmp_t / test_count << " ms." << std::endl;
        std::cout << "simd_openmp_t = " << simd_openmp_t / test_count << " ms." << std::endl;

        x.push_back(double(point_size));
        none_y.push_back(none_t);
        simd_y.push_back(simd_t);
        openmp_y.push_back(openmp_t);
        simd_openmp_y.push_back(simd_openmp_t);
    }
  
    std::map<std::string, std::string> pcl_result;
    std::map<std::string, std::string> simd_result;
    std::map<std::string, std::string> openmp_result;
    std::map<std::string, std::string> simd_openmp_result;
    pcl_result.insert(std::pair<std::string, std::string>("color", "red"));
    pcl_result.insert(std::pair<std::string, std::string>("label", "pcl-default"));
    simd_result.insert(std::pair<std::string, std::string>("color", "green"));
    simd_result.insert(std::pair<std::string, std::string>("label", "simd-optimization"));
    openmp_result.insert(std::pair<std::string, std::string>("color", "blue"));
    openmp_result.insert(std::pair<std::string, std::string>("label", "openmp-optimization"));
    simd_openmp_result.insert(std::pair<std::string, std::string>("color", "black"));
    simd_openmp_result.insert(std::pair<std::string, std::string>("label", "simd&openmp-optimization"));
    plt::plot(x, none_y, pcl_result);
    plt::plot(x, simd_y, simd_result);
    plt::plot(x, openmp_y, openmp_result);
    plt::plot(x, simd_openmp_y, simd_openmp_result);
    plt::xlabel("point size");
    plt::ylabel("cost time:ms");
    plt::legend();
    plt::title("compute centroid performance");
    plt::show();

    return 0;
}
````

- 测试结果：
![centroid.png][1]

从结果可以看到，openmp优化结果最好，结合simd与openmp优化的效果最差，起到负优化的作用。


## 完整测试代码

```cpp
/**
 * @file centroid.cpp
 * @author mango (you@domain.com)
 * @brief Simd
 * @version 0.1
 * @date 2021-10-24
 * 
 * @copyright Copyright (c) 2021
 * 
 */

#include <random>
#include <iostream>
#include <vector>
#include <chrono>

#include "pcl/point_cloud.h"
#include "pcl/point_types.h"
#include "pcl/common/centroid.h"

#include "opencv2/core/simd_intrinsics.hpp"

#include "omp.h"

#include "matplotlibcpp.h"

namespace plt = matplotlibcpp;

void GetCentroidSimd(pcl::PointCloud<pcl::PointXYZ>::ConstPtr cloud, pcl::PointXYZ& centroid)
{
    if(cloud->empty()) {return;}

    int count = cloud->size();
    cv::v_float32 centroidx = cv::vx_setzero_f32();
    cv::v_float32 centroidy = cv::vx_setzero_f32();
    cv::v_float32 centroidz = cv::vx_setzero_f32();
    int step = cv::v_float32::nlanes;

    for (int i = 0; i < count; i += step)
    {
          //固定长方法
          //cv::v_float32 vx(cloud->points[i].x, cloud->points[i + 1].x, cloud->points[i + 2].x, cloud->points[i + 3].x);
          //cv::v_float32 vy(cloud->points[i].y, cloud->points[i + 1].y, cloud->points[i + 2].y, cloud->points[i + 3].y);
          //cv::v_float32 vz(cloud->points[i].z, cloud->points[i + 1].z, cloud->points[i + 2].z, cloud->points[i + 3].z);
          cv::v_float32 vx;
          cv::v_float32 vy;
          cv::v_float32 vz;
          cv::v_float32 vs;//占位数据
          cv::v_load_deinterleave(&cloud->points[i].x, vx, vy, vz, vs);

          centroidx += vx;
          centroidy += vy;
          centroidz += vz;
    }
    centroid.x = cv::v_reduce_sum(centroidx);
    centroid.y = cv::v_reduce_sum(centroidy);
    centroid.z = cv::v_reduce_sum(centroidz);
    for (size_t i = count - count % step; i < count; i++)
    {
        centroid.x += cloud->points[i].x;
        centroid.y += cloud->points[i].y;
        centroid.z += cloud->points[i].z;
    }
    centroid.x /= count;
    centroid.y /= count;
    centroid.z /= count;
}

void GetCentroidSimdWithOpenMP(pcl::PointCloud<pcl::PointXYZ>::ConstPtr cloud, pcl::PointXYZ& centroid)
{
    if (cloud->empty()) { return; }

    int count = cloud->size();
    cv::v_float32x4 centroidx = cv::v_setzero_f32();
    cv::v_float32x4 centroidy = cv::v_setzero_f32();
    cv::v_float32x4 centroidz = cv::v_setzero_f32();
    int step = cv::v_float32x4::nlanes;

    //#pragma omp parallel for reduction(+:centroidx,centroidy,centroidz) : 错误，v_float32类型不可做reduction，下面手动reduction
    //std::vector<cv::v_float32x4> centroidx_arr(4, cv::v_setzero_f32());
    //std::vector<cv::v_float32x4> centroidy_arr(4, cv::v_setzero_f32());
    //std::vector<cv::v_float32x4> centroidz_arr(4, cv::v_setzero_f32());

    std::array<cv::v_float32x4, 4> centroidx_arr;
    std::array<cv::v_float32x4, 4> centroidy_arr;
    std::array<cv::v_float32x4, 4> centroidz_arr;
#pragma omp parallel for
    for (int i = 0; i < count; i += step)
    {
        cv::v_float32x4 vx;
        cv::v_float32x4 vy;
        cv::v_float32x4 vz;
        cv::v_float32x4 vs;//占位数据
        cv::v_load_deinterleave(&cloud->points[i].x, vx, vy, vz, vs);
        
        int id = omp_get_thread_num();
        centroidx_arr[id] += vx;
        centroidy_arr[id] += vy;
        centroidz_arr[id] += vz;
    }
    //手动reduction
    for (auto& a : centroidx_arr){ centroidx += a; }
    for (auto& a : centroidy_arr){ centroidy += a; }
    for (auto& a : centroidz_arr){ centroidz += a; }
   
    centroid.x = cv::v_reduce_sum(centroidx);
    centroid.y = cv::v_reduce_sum(centroidy);
    centroid.z = cv::v_reduce_sum(centroidz);
    for (size_t i = count - count % step; i < count; i++)
    {
        centroid.x += cloud->points[i].x;
        centroid.y += cloud->points[i].y;
        centroid.z += cloud->points[i].z;
    }
    centroid.x /= count;
    centroid.y /= count;
    centroid.z /= count;
}

void GetCentroidOpenMP(pcl::PointCloud<pcl::PointXYZ>::ConstPtr cloud, pcl::PointXYZ& centroid)
{
    if (cloud->empty()) { return; }

    int count = cloud->size();

    float x_sum = 0;
    float y_sum = 0;
    float z_sum = 0;
#pragma omp parallel for reduction(+:x_sum,y_sum,z_sum)
    for (int i = 0; i < count; i++)
    {
        x_sum += cloud->points[i].x;
        y_sum += cloud->points[i].y;
        z_sum += cloud->points[i].z;
    }
    centroid.x = x_sum /count;
    centroid.y = y_sum /count;
    centroid.z = z_sum /count;
}

int main(int argc, char** argv)
{
    pcl::PointCloud<pcl::PointXYZ>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZ>);
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_real_distribution<float> dist(-1000.0f, 1000.0f);

    std::vector<double> x;
    std::vector<double> none_y;
    std::vector<double> simd_y;
    std::vector<double> openmp_y;
    std::vector<double> simd_openmp_y;

    for (size_t p = 1; p < 8; p++)
    {
        size_t point_size = std::pow(10, p);
        double none_t = 0;
        double simd_t = 0;
        double openmp_t = 0;
        double simd_openmp_t = 0;
        size_t test_count = 10;
        for (size_t k = 0; k < test_count; k++)
        {
            cloud->clear();
            for (size_t i = 0; i < point_size; i++)
            {
                cloud->push_back(pcl::PointXYZ(dist(gen), dist(gen), dist(gen)));
            }

            pcl::PointXYZ centroid;
            auto t0 = std::chrono::system_clock::now();
            pcl::computeCentroid(*cloud, centroid);
            auto t1 = std::chrono::system_clock::now();
            GetCentroidSimd(cloud, centroid);
            auto t2 = std::chrono::system_clock::now();
            GetCentroidOpenMP(cloud, centroid);
            auto t3 = std::chrono::system_clock::now();
            GetCentroidSimdWithOpenMP(cloud, centroid);
            auto t4 = std::chrono::system_clock::now();
            none_t += std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0).count();
            simd_t += std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count();
            openmp_t += std::chrono::duration_cast<std::chrono::milliseconds>(t3 - t2).count();
            simd_openmp_t += std::chrono::duration_cast<std::chrono::milliseconds>(t4 - t3).count();
        }

        std::cout << "none_t = " << none_t / test_count << " ms." << std::endl;
        std::cout << "simd_t = " << simd_t / test_count << " ms." << std::endl;
        std::cout << "openmp_t = " << openmp_t / test_count << " ms." << std::endl;
        std::cout << "simd_openmp_t = " << simd_openmp_t / test_count << " ms." << std::endl;

        x.push_back(double(point_size));
        none_y.push_back(none_t);
        simd_y.push_back(simd_t);
        openmp_y.push_back(openmp_t);
        simd_openmp_y.push_back(simd_openmp_t);
    }
  
    std::map<std::string, std::string> pcl_result;
    std::map<std::string, std::string> simd_result;
    std::map<std::string, std::string> openmp_result;
    std::map<std::string, std::string> simd_openmp_result;
    pcl_result.insert(std::pair<std::string, std::string>("color", "red"));
    pcl_result.insert(std::pair<std::string, std::string>("label", "pcl-default"));
    simd_result.insert(std::pair<std::string, std::string>("color", "green"));
    simd_result.insert(std::pair<std::string, std::string>("label", "simd-optimization"));
    openmp_result.insert(std::pair<std::string, std::string>("color", "blue"));
    openmp_result.insert(std::pair<std::string, std::string>("label", "openmp-optimization"));
    simd_openmp_result.insert(std::pair<std::string, std::string>("color", "black"));
    simd_openmp_result.insert(std::pair<std::string, std::string>("label", "simd&openmp-optimization"));
    plt::plot(x, none_y, pcl_result);
    plt::plot(x, simd_y, simd_result);
    plt::plot(x, openmp_y, openmp_result);
    plt::plot(x, simd_openmp_y, simd_openmp_result);
    plt::xlabel("point size");
    plt::ylabel("cost time:ms");
    plt::legend();
    plt::title("compute centroid performance");
    plt::show();

    return 0;
}
```
-----------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/opencv/pcl-compute-centroid-optimization-benchmark.html

  [1]: https://mangoroom.cn/usr/uploads/2021/10/2913189785.png