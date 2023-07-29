---
title: "hpc优化for循环分支预测2"
categories: [ "c++","HPC" ]
tags: [ "c++","for","hpc","if","else" ]
draft: false
slug: "cpp/hpc-branch-prediction-loop-for02"
date: "2022-05-23 00:06:00"
---

上一篇中提到分支预测的优化方法，主要是通过预先排序分支条件的方式提高分支预测命中率的。除此外，还有替代和消除ifelse分支的优化思路可以采用。

比如有如下的for循环中存在分支判断

```cpp
std::vector<int> a(test_size);
std::vector<int> b(test_size);
std::vector<int> c(test_size);
for(int i = 0; i < test_size; ++i)
{
    if(a[i] > b[i])
    {
        c[i] = a[i] - b[i];
    }else
    {
        c[i] = b[i] - a[i];
    }
}
```
## 三目运算符替代if-else

编程语言除了ifelse的分支判断写法外，还有三目运算符可以完成分支判断的写法，基于三目运算的分支跳转在一些编译器优化下往往会比ifelse的效率有所提高,将上述例子改为三目运算符为：

```cpp
//使用三目运算符替代if-else
for(int i = 0; i < test_size; ++i)
{
    c[i] = a[i] > b[i] ? a[i] - b[i] : b[i] - a[i];
}
```

## 调整程序逻辑消除if-else

三目运算符本质上还是要有分支跳转，其实很多程序在保证结果不变的情况下，有时候是可以通过调整程序实现逻辑从而避免使用分支跳转的。比如以上例子，不难看出实际上就是计算a与b的差值的绝对值而已，那么程序就可以改由为以下不包含分支跳转的。

```cpp
 for(int i = 0; i < test_size; ++i)
{
    c[i] = std::abs(a[i] - b[i]);
}
```

## 测试程序

```cpp
/**
 * @file loop_for01.cpp
 * @author mango (you@domain.com)
 * @brief 高性能优化-分支预测-for循环
 * @version 0.1
 * @date 2022-05-19
 * 
 * @copyright Copyright (c) 2022
 * 
 */

 #include <vector>
 #include <random>
 #include <algorithm>
 #include <chrono>
 #include <iostream>


 int main(int argc, char** argv)
 {
     std::random_device rd;
     std::mt19937 gen(rd());
     std::uniform_int_distribution<int> dist(0, 255);
    
     for(int k = 2; k <= 7; ++k)
     {
        int test_size = std::pow(10, k);
        std::cout << "-------------------------test size :" << test_size << " -----------------------" << std::endl;
        std::vector<int> a(test_size);
        std::vector<int> b(test_size);
        std::vector<int> c(test_size);

        for(int i = 0; i < test_size; ++i)
        {
            a[i] = dist(gen);
            b[i] = dist(gen);
        }

        auto t0 = std::chrono::system_clock::now();
        //原始条件判断运算
        for(int i = 0; i < test_size; ++i)
        {
            if(a[i] > b[i])
            {
                c[i] = a[i] - b[i];
            }else
            {
                c[i] = b[i] - a[i];
            }
        }
        auto t1 = std::chrono::system_clock::now();
        //使用三目运算符替代if-else
        for(int i = 0; i < test_size; ++i)
        {
            c[i] = a[i] > b[i] ? a[i] - b[i] : b[i] - a[i];
        }

        auto t2 = std::chrono::system_clock::now();
        //改变程序逻辑完成if-else替代
        for(int i = 0; i < test_size; ++i)
        {
            c[i] = std::abs(a[i] - b[i]);
        }
        auto t3 = std::chrono::system_clock::now();
        
        auto dt1 = std::chrono::duration_cast<std::chrono::microseconds>(t1 - t0).count();
        auto dt2 = std::chrono::duration_cast<std::chrono::microseconds>(t2 - t1).count();
        auto dt3 = std::chrono::duration_cast<std::chrono::microseconds>(t3 - t2).count();

        std::cout << "dt1 = "<< dt1 << " us"<< std::endl;
        std::cout << "dt1 = "<< dt2 << " us"<< std::endl;
        std::cout << "dt3 = "<< dt3 << " us"<< std::endl;
     }
   

     return 0;
 }
```

在releae模式下运行结果如下

```
-------------------------test size :100 -----------------------
dt1 = 0 us
dt1 = 0 us
dt3 = 0 us
-------------------------test size :1000 -----------------------
dt1 = 0 us
dt1 = 0 us
dt3 = 0 us
-------------------------test size :10000 -----------------------
dt1 = 4 us
dt1 = 3 us
dt3 = 3 us
-------------------------test size :100000 -----------------------
dt1 = 103 us
dt1 = 83 us
dt3 = 102 us
-------------------------test size :1000000 -----------------------
dt1 = 1015 us
dt1 = 910 us
dt3 = 999 us
-------------------------test size :10000000 -----------------------
dt1 = 9203 us
dt1 = 9156 us
dt3 = 9091 us
```

可以看到相比原始使用ifelse的方法效率有小幅提升

