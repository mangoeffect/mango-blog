---
title: "RANSAC随机抽样一致性直线拟合"
categories: [ "opencv","算法","计算机视觉" ]
tags: [ "opencv","拟合","ransac","line-fitting" ]
draft: false
slug: "opencv/line-fitting-based-ransac"
date: "2022-01-20 18:29:00"
---

## 概要

本文介绍基于ransac(随机抽样一致性)的直线拟合方法，涵盖一下的内容。

- ransac的算法思想
- ransac的算法步骤
- 如何调整ransac算法迭代的次数
- 基于opencv编码实现

## ransac算法流程

RANSAC(RANdom SAmple Consensus)，即随机采样一致性。该方法最早是由Fischler和Bolles提出的一种鲁棒估计方法，最早用于计算机视觉中位姿估计问题，现在已广泛应用于已知模型的参数估计问题中。对于数据中存在大比例外点（错误数据、野值点）时，RANSAC方法十分有效。其思想比较直观和容易理解，即可从坐标点中随机抽取两点，计算一条直线，然后判断所有的点与该直线的吻合程度，不断重复直到挑选出最好的。

![ransac-line-fit](https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com/mango-blog-imagesransac-linefit.gif)

### 算法流程

- 1 从所有点中**随机**抽取两个坐标点，计算两点的直线方程
- 2 基于一个距离阈值t, 遍历所有点，点与步骤1所得直线距离小于t的则为**内点**，否则为**离群点**
- 3 不断重复步骤1-2，记录每次所得的**内点**数量以及内点，重复N次结束迭代
- 4 选择迭代过程中所**内点**数量最大的一次迭代结果，基于该次结果的内点做最终的直线拟合

### 采样迭代次数确定

关于迭代多少次比较合适ransac[原论文](https://www.cct.lsu.edu/~kzhang/papers/RANSAC.pdf)中有介绍，这里直接给出公式

$$N = \frac{\log(1 - z)}{\log{(1 - w^n)}}$$

其中，K为需要采样的次数；z为获取一个好样本的概率，一般设为99%；w为点集中内点的比例，一般可以在初始时设置一个较小值，如0.1，然后迭代更新；n为模型参数估计需要的最小点个数，直线拟合最少需要2个点。

## 编码实现

下面基于OpenCV编码实现

```cpp
#include "opencv2/opencv.hpp"

#include <iostream>
#include <vector>
#include <random>


std::tuple<int, int> GetSample(const int& index_size,
                               const std::vector<std::tuple<int, int>>& sampled_indexes)
{
    assert(index_size > 2);
    static std::random_device rd;
    static std::mt19937_64 gen(rd());
    static std::uniform_int_distribution<int> dist(0, index_size - 1);

    while (true)
    {
        int index1 = dist(gen);
        int index2 = dist(gen);
        if (index1 == index2) { continue; }
        int min_index = std::min(index1, index2);
        int max_index = std::max(index1, index2);
        bool has_sampled = false;
        for (auto& [i, j] : sampled_indexes)
        {
            if (min_index == i && j == max_index)
            {
                has_sampled = true;
                break;
            }
        }
        if (has_sampled) { continue; }
        else { return { min_index, max_index }; }
    } 
}

void Show(const std::vector<cv::Point2f>& all_pts,
    const std::vector<cv::Point2f>& inlier_pts,
    const std::vector<cv::Point2f>& sample_pts,
    const int windows_size,
    const double a,
    const double b,
    const double c)
{
    cv::Mat image = cv::Mat(windows_size, windows_size, CV_8UC3, cv::Scalar::all(255));
    
    for (auto& p : all_pts)
    {
        if (p.x >= 0 && p.x < windows_size && p.y >= 0 && p.y < windows_size)
        {
            cv::circle(image, p, 5, { 0, 0, 255 }, -1);
        }
    }

    cv::Point2f p1, p2;

    if (a * b > 0)
    {
        p1.x = windows_size - 1;
        p1.y = -(a * p1.x + c) / b;
        p2.x = 0;
        p2.y = -(a * p2.x + c) / b;

    }else
    {
        p1.y = 0;
        p1.x = -(b * p1.y + c) / a;
        p2.y = windows_size - 1;
        p2.x = -(b * p2.y + c) / a;
    }

    cv::line(image, p1, p2, { 0, 0, 0 }, 2, cv::LINE_AA);

    for (auto& p : inlier_pts)
    {
        if (p.x >= 0 && p.x < windows_size && p.y >= 0 && p.y < windows_size)
        {
            cv::circle(image, p, 5, { 255, 0, 0 },-1);
        }
    }

    for (auto& p : sample_pts)
    {
        if (p.x >= 0 && p.x < windows_size && p.y >= 0 && p.y < windows_size)
        {
            cv::circle(image, p, 5, { 0, 255, 0 }, -1);
        }
    }

    cv::imshow("ransac-line-fit", image);
    cv::waitKey(500);
}

std::tuple<double, double, double> LineFit(const std::vector<cv::Point2f>& pts)
{
    cv::Vec4f line;
    cv::fitLine(pts, line, cv::DIST_L2, 0, 1e-2, 1e-2);
    double a = line[1];
    double b = -line[0];
    double c = line[0] * line[3] - line[1] * line[2];
    return { a, b, c };
}

void RansacLinefit(const std::vector<cv::Point2f>& pts,
    const float inlier_threshold,
    std::vector<int>& inlier_indexes)
{
    if (pts.size() <= 3) { return; }
    if (inlier_threshold < 1e-6) { return; }

    //1. 参数检查，初始化
    int iterate_nums = 500;                     //迭代次数
    float sample_points_min_distance = 5.0f;    //两个采样点之间最小的距离
    std::vector<std::tuple<int, int>> sampled_indexes;//已经采样的坐标点
    sampled_indexes.reserve(iterate_nums);            
    std::vector<int> is_inlier(pts.size(), 0);
    std::vector<int> is_inlier_tmp(pts.size(), 0);
    int max_inlier_num = 0;
    int sample_count = 0;
    //2. 循环迭代
    while (sample_count < iterate_nums)
    {
        //3. 随机抽取两点(采样）
        auto [p1, p2] = GetSample(pts.size(), sampled_indexes);
        if (std::abs(pts[p1].x - pts[p2].x) < sample_points_min_distance
            && std::abs(pts[p1].y - pts[p2].y) < sample_points_min_distance)
        {
            continue;
        }
        else
        {
            sampled_indexes.push_back({ p1, p2 });
        }
        //4. 直线拟合
        auto [a, b, c] = LineFit({ pts[p1], pts[p2] });


        //5. 基于拟合的直线区分内外点
        int inlier_num = 0;
        std::vector<cv::Point2f> inliers;

        for (int i = 0; i < pts.size(); i++)
        {
            auto& p = pts[i];
            is_inlier_tmp[i] = 0;
            if (std::abs(a * p.x + b * p.y + c) < inlier_threshold)
            {
                is_inlier_tmp[i] = 1;
                inlier_num++;
                inliers.push_back(pts[i]);
            }
        }
        Show(pts, inliers, { { pts[p1], pts[p2] } }, 500, a, b, c);

        if (inlier_num > max_inlier_num)
        {
            max_inlier_num = inlier_num;
            is_inlier = is_inlier_tmp;
        }
        //6. 更新迭代的最佳次数
        if (inlier_num == 0)
        {
           iterate_nums = 500;
        }
        else
        {
            double epsilon = 1.0 - double(inlier_num) / (double)pts.size(); //野值点比例
            double p = 0.99;                                                //所有样本中存在1个好样本的概率
            double s = 2.0;
            iterate_nums = int(std::log(1.0 - p) / std::log(1.0 - std::pow((1.0 - epsilon), s)));
        }
        sample_count++;
    }

    //7. 基于最优的结果所对应的内点做最终拟合
    std::vector<cv::Point2f> inliers;
    inliers.reserve(max_inlier_num);
    for (int i = 0; i < is_inlier.size(); i++)
    {
        if (1 == is_inlier[i])
        {
            inliers.push_back(pts[i]);
        }
    }
    auto [a, b, c] = LineFit(inliers);
    std::cout << " a = " << a << " b = " << b << " c = " << c << std::endl;
    Show(pts, inliers, {},  500, a, b, c);
    cv::waitKey(0);
}

int main()
{
    double a = 1.0, b = 2.0, c = -800.0;
    std::random_device rd;
    std::mt19937_64 gen(rd());
    std::uniform_int_distribution<int> dist(0, 499);
    std::normal_distribution<double> disty(0, 50);
    std::normal_distribution<double> distx(0, 10);
    std::vector<cv::Point2f> pts;
    for (int i = 1; i < 50; i ++)
    {
        float x = dist(gen);
        float y = -(a * x + c) / b;
        if (i % 2 != 0)
        {
            x += distx(gen);
            y += disty(gen);
        }
        pts.emplace_back(x, y);
    }
    std::vector<int> inliers_indexes;
    RansacLinefit(pts, 10, inliers_indexes);
    return 0;
}
```


--------

本文由芒果浩明发布，转载请注明出处。
本文链接：[https://mangoroom.cn/opencv/line-fitting-based-ransac.html](https://mangoroom.cn/opencv/line-fitting-based-ransac.html)