---
title: "hpc优化for循环分支预测1"
categories: [ "c++","HPC" ]
tags: [ "c++","for","hpc","if","else" ]
draft: false
slug: "hpc-branch-prediction-loop-for01"
date: "2022-05-19 18:28:54"
---


## 预先排序条件 

假如有以下for循环，for内部有if-else分支判断

```cpp
for(int i = 0; i < task_size; ++i)
{
    if(tasks[i].condi > 128)
    {
        tasks[i].RunPro1();
    }else
    {
        tasks[i].RunPro2();
    }
}
```
其中判断条件也是一个数组，那么可以通过预先将判断条件进行排序，让进入循环前条件数组已经是一个有序的数组。有序的条件数组可以提高分支预测的准确率

```cpp
std::sort(tasks_sorted.begin(), tasks_sorted.end(), [](const Task& t1, const Task& t2){ return t1.condi < t2.condi;});
for(int i = 0; i < task_size; ++i)
{
    if(tasks_sorted[i].condi < 128)
    {
        tasks_sorted[i].RunPro2();
    }else
    {
        tasks_sorted[i].RunPro1();
    }
}
```

**但此方法需要注意一个问题：** 排序也是占用时间消耗的，所以需要判断循环内的处理是否比较复杂是否耗时长，足以抵消预先排序带来的消耗，如果排序耗时比for循环内部处理都要长了则无需考虑这个优化方法。

## 测试代码

```cpp
/**
 * @file loop_for01.cpp
 * @author mango (you@domain.com)
 * @brief 高性能优化-分支预测-for循环
 * @version 0.1
 * @date 2022-05-18
 * 
 * @copyright Copyright (c) 2022
 * 
 */

 #include <vector>
 #include <random>
 #include <algorithm>
 #include <chrono>
 #include <thread>
 #include <iostream>

 struct Task
 {
     int condi;
     std::vector<double> vec;
     void RunPro1()
     {
         //复杂运算
         std::sort(vec.begin(), vec.end());
     };
     void RunPro2()
     {
         std::cout << vec.size() << std::endl;
     };
 };

 int main(int argc, char** argv)
 {
     std::random_device rd;
     std::mt19937 gen(rd());
     std::uniform_int_distribution<int> dist(0, 255);
     std::uniform_real_distribution<double> dist2(10.0f, 1000.0);

     const size_t task_size = 1e3;
     std::vector<Task> tasks(task_size);
     for(int i = 0; i < task_size; ++i)
     {
         tasks[i].condi = dist(gen);
         for(int j = 0; j < 10000; j++)
         {
             tasks[i].vec.push_back(dist2(gen));
         }
     }
     auto tasks_sorted = tasks;

     auto t0 = std::chrono::system_clock::now();
     std::sort(tasks_sorted.begin(), tasks_sorted.end(), [](const Task& t1, const Task& t2){ return t1.condi < t2.condi;});
     auto t1 = std::chrono::system_clock::now();

     for(int i = 0; i < task_size; ++i)
     {
         if(tasks_sorted[i].condi < 128)
         {
             tasks_sorted[i].RunPro2();
         }else
         {
             tasks_sorted[i].RunPro1();
         }
     }
    auto t2 = std::chrono::system_clock::now();
    for(int i = 0; i < task_size; ++i)
     {
         if(tasks[i].condi > 128)
         {
             tasks[i].RunPro1();
         }else
         {
             tasks[i].RunPro2();
         }
     }
     auto t3 = std::chrono::system_clock::now();
     
     auto dt1 = std::chrono::duration_cast<std::chrono::microseconds>(t1 - t0).count();
     auto dt2 = std::chrono::duration_cast<std::chrono::microseconds>(t2 - t1).count();
     auto dt3 = std::chrono::duration_cast<std::chrono::microseconds>(t3 - t2).count();

     std::cout << "sort cost "<< dt1 << " us"<< std::endl;
     std::cout << "dt1 = "<< dt2 << " us"<< std::endl;
     std::cout << "dt2 = "<< dt3 << " us"<< std::endl;

     return 0;
 }
```