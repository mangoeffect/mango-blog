---
title: "opencv统一simd框架使用(一）"
categories: [ "opencv","c++" ]
tags: [ "opencv","avx","simd","universal-inrinsics","sse","neo" ]
draft: false
slug: "opencv/opencv-universal-intrinsics-simd-1"
date: "2021-10-18 19:21:00"
---

## opencv universalsimd 简介

单指令多数据（SIMD，SingleInstruction Multiple Data）是非常强大的优化程序效率方法，顾名思义即可用一条指令运算多个数据，从而提升程序运行效率。举个例子，
假如有以下两个数组a、b,要求计算a、b数组一一对应相加结果存到c数组：

```
a = {1, 2, 3, 4}
b = {4, 3, 2, 1}
```
正常程序写法就是循环遍历a、b数组，一一对应相加存放到c

```cpp
for(int i = 0; i < 4; i++)
{
    c[i] = a[i] + b[i];
}
```
但是simd可以这么做

```cpp
//.. 将a、b一次性加载到va、vb
vc = va + vb;//一次运算4个数字相加
//..计算结果vc一次性存储至数组c
```

OpenCV 4.x(3.4.x版本以上也有)中提供了强大的统一向量指令（universal intrinsics），使用这些指令可以方便地为算法提速，计算密集型任务皆可使用这套指令加速，目前OpenCV源码算法都在用这套指令集框架进行优化。使用这套指令集感受最大的有点便是以下两点：

- **跨平台**：此跨平台指的是cpu指令集的跨平台，不同cpu提供不一样的加速指令集，如x86有sse、avx等，arm有neno，以及新出的Risc-V也有自己的指令集。有了OpenCV统一的Simd指令集库，就可以避免每个硬件平台单独优化算法的麻烦。
- **自适应向量化内存位宽**: 即便是相同架构的cpu也有不同的指令集标准，如早期x86的128bit位宽的sse，发展到后面有256bit位宽的avx,以及扩展到512bit的avx512。这意味着如果编写的代码有可能存在浪费cpu算力的情况（在运行机器可以支持更高的位宽，但是你只用了更低的），或者存在使用指令集标准过新导致运行设备不支持的情况。否则就需要编写面向多个标准的多套加速代码。

## 使用方法

- 包含头文件

```cpp
#include "opencv2/core/simd_intrinsics.hpp"
```

- 编译选项勾选simd加速选项

Visual Studio 设置方法：属性->C/C++->代码生成->启用增强指令集

Linux Gcc编译选项设置：编译加上-mavx2、等选项


### 检查opencv支持simd信息

```cpp
//simd信息获取输出
void PrintOpenCVInfo()
{
    std::cout<<"--------------------------OpenCV informaintion--------------------------"<< std::endl;
    //OpenCV版本，Universal Simd在3.4.x版本就开始提供，不过建议使用4.x版本
    std::cout << "OpenCV version:" << cv::getVersionString() << std::endl;
    std::cout << "Simd info: "<< std::endl;
#ifdef CV_SIMD
    //是否支持simd优化
    std::cout << "CV_SIMD : " << CVAUX_STR(CV_SIMD)  << std::endl;
    //simd优化内存位宽
    std::cout << "CV_SIMD_WIDTH : " <<  CVAUX_STR(CV_SIMD_WIDTH) << std::endl;
    //128bit位宽优化是否支持，绝大部分x86架构cpu支持，如常用的sse指令集
    std::cout << "CV_SIMD128 : " <<CVAUX_STR(CV_SIMD128) << std::endl;
    //256bit位宽优化是否支持，大部分近几年的x86架构cpu支持， avxz指令集
    std::cout << "CV_SIMD256: " <<CVAUX_STR(CV_SIMD256) << std::endl;
    //512bit位宽是否支持，这个intel最先搞得，近几年的intel cpu与最新的AMD锐龙支持
    std::cout << "CV_SIMD512 : " CVAUX_STR(CV_SIMD512) << std::endl;
#else
    std::cout << "CV_SIMD is NOT defined." << std::endl;
#endif

#ifdef CV_SIMD
    std::cout << "sizeof(v_uint8) = " << sizeof(cv::v_uint8) << std::endl;
    std::cout << "sizeof(v_int32) = " << sizeof(cv::v_int32) << std::endl;
    std::cout << "sizeof(v_float32) = " << sizeof(cv::v_float32) << std::endl;
#endif
}

```

### 简单使用simd

下面代码介绍了

```cpp
//加载-计算-存储优化步骤
void SimdSample()
{
    std::cout << "----------------------OpenCV Simd simple sample--------------------------" << std::endl;
    
    // 构造一个常量
    cv::v_int32x4 v_c1(1, 1, 1, 1);             //方法一
    cv::v_int32x4 v_c2 = cv::v_setall(1);       //方法二
    cv::v_int32x4 v_c3 = cv::v_setzero_s32();   //方法三，但只能设置0

    // 构造一个simd长度的数组
    cv::v_int32x4 v_a(1, 2, 3, 4);
    cv::v_int32x4 v_b(1, 2, 3, 4);
    //v_a = [ a0, a1, a2, a3]
    //v_b = [ b0, b1, b2, b3]
    //v_c = [a0+b0, a1+b1, a2+b2, a3+b3]
    cv::v_int32x4 v_c = v_a + v_b;
    PrintVector(v_c, "v_c");
    
    //--------------数组循环优化示例--------------------
    const size_t  array_size = 400;
    int* int_array_a = new int[array_size];
    int* int_array_b = new int[array_size];
    int* int_array_c = new int[array_size];

    //原循环程序计算
    for (int i = 0; i < array_size; i++)
    {
        int_array_c[i] = int_array_a[i] * int_array_b[i];
    }

    //Simd优化:已知编译器向量化内存宽度（如常用的sse为128bit的向量化内存位宽）
    for (int i = 0; i < array_size; i += 4)
    {
        //加载
        cv::v_int32x4 v_a = cv::v_load(int_array_a + i);//一次从数组中读取4个int变量存储到v_a
        cv::v_int32x4 v_b = cv::v_load(int_array_b + i);//一次从数组中读取4个int变量存储到v_b
        //计算
        cv::v_int32x4 v_c = v_a * v_b;//一次性计算4个变量乘法
        //v_a = [ a0, a1, a2, a3]
        //v_b = [ b0, b1, b2, b3]
        //v_c = [a0*b0, a1*b1, a2*b2, a3*b3]
        //储存
        cv::v_store(int_array_c + i, v_c);
    }

    //Simd自适位宽优化：未知编译器采用的向量化位宽
    for (size_t i = 0; i < array_size; i += cv::v_int32::nlanes)
    {
        //加载 n = 向量化内存位宽 / sizeof(int),如sse是 128 / 32 = 4, avx2 是256 / 32 = 8
        cv::v_int32 v_a = cv::vx_load(int_array_a + i);//一次从数组中读取n个int变量存储到v_a
        cv::v_int32 v_b = cv::vx_load(int_array_b + i);//一次从数组中读取n个int变量存储到v_b
        //计算
        cv::v_int32 v_c = v_a * v_b;//一次性计算n个变量乘法
        //v_a = [ a0, a1, a2, a3]
        //v_b = [ b0, b1, b2, b3]
        //v_c = [a0*b0, a1*b1, a2*b2, a3*b3]
        //储存
        cv::vx_store(int_array_c + i, v_c);
    }
}
```

### 常见类型数组for循环优化

```cpp
//uchar short int float类型数组优化
void BasicDataArrayOptimization()
{
    std::cout << "----------------------OpenCV Simd simple sample--------------------------" << std::endl;

    //uchar
    const size_t  array_size = 4000;
    uchar* uchar_array_a = new uchar[array_size];
    uchar* uchar_array_b = new uchar[array_size];
    uchar* uchar_array_c = new uchar[array_size];

    //一次性加载16个uchar数据进行计算，uchar占8bit, 8x16=128bit
    for (size_t i = 0; i < array_size; i += 16)
    {
        //加载
        cv::v_uint8x16 v_a = cv::v_load(uchar_array_a + i);
        cv::v_uint8x16 v_b = cv::v_load(uchar_array_b + i);
        //计算...
        cv::v_uint8x16 v_c = v_a + v_b;

        //存储
        cv::v_store(uchar_array_c + i, v_c);
    }

    //short
    short* short_arrary_a = new short[array_size];
    short* short_arrary_b = new short[array_size];
    short* short_arrary_c = new short[array_size];
    
    //一次性加载8个short数据进行计算，short占16bit, 16x8=128bit
    for (size_t i = 0; i < array_size; i += 8)
    {
        //加载
        cv::v_int16x8 v_a = cv::v_load(short_arrary_a + i);
        cv::v_int16x8 v_b = cv::v_load(short_arrary_b + i);
        //计算
        cv::v_int16x8 v_c = v_a + v_b;
        //存储
        cv::v_store(short_arrary_c + i, v_c);
    }


    //float
    float* float_array_a = new float[array_size];
    float* float_array_b = new float[array_size];
    float* float_array_c = new float[array_size];
    
    //一次性加载4个float数据进行计算，float占32bit, 32x4=128bit
    for (size_t i = 0; i < array_size; i += 4)
    {
        //加载
        cv::v_float32x4 v_a = cv::v_load(float_array_a + i);
        cv::v_float32x4 v_b = cv::v_load(float_array_a + i);
        //计算
        cv::v_float32x4 v_c = v_a + v_b;
        //存储
        cv::v_store(float_array_c + i, v_c);
    }
}
```

### 数组元素个数非位宽整数倍

```cpp
//顺组对齐示例，长度不是向量优化位宽时倍数
void LoopLenghtAligned()
{
    const size_t  array_size = 4007;
    uchar* uchar_array_a = new uchar[array_size];
    uchar* uchar_array_b = new uchar[array_size];
    uchar* uchar_array_c = new uchar[array_size];

    //一次性加载16个uchar数据进行计算，uchar占8bit, 8x16=128bit
    size_t i = 0;
    for (; i < array_size - 16; i += 16)
    {
        //加载
        cv::v_uint8x16 v_a = cv::v_load(uchar_array_a + i);
        cv::v_uint8x16 v_b = cv::v_load(uchar_array_b + i);
        //计算...
        cv::v_uint8x16 v_c = v_a + v_b;

        //存储
        cv::v_store(uchar_array_c + i, v_c);
    }
    for (; i < array_size; i++)
    {
        uchar_array_c[i] = uchar_array_a[i] + uchar_array_b[i];
    }
}

```

### 输出单个simd变量

```cpp
//输出向量
template<typename T>
void PrintVector(const T& simd_vector, const std::string& name)
{
    //获取向量数组长度
    int arr_size = int(T::nlanes);
    //申请对应类型c风格数组
    T::lane_type* arr = new T::lane_type[arr_size];
    //将向量装载到c风格数组
    cv::v_store(arr, simd_vector);

    //打印输出
    std::cout << name << " : ";
    for (size_t i = 0; i < arr_size; i++)
    {
        std::cout << arr[i] << " ";
    }   
    std::cout << std::endl;
}
```

### 数学运算方法

```cpp
//数学函数
void MathematicsFunctionSample()
{
    std::cout << "-----------------------Mathematics function sample------------------------------" << std::endl;
    cv::v_float32 v_float_arr(1.3, 1.4, 1.5, 1.7);

    //向下取整，处理结果为1 1 1 1
    cv::v_int32x4 v_int_arr = cv::v_floor(v_float_arr);
    PrintVector<cv::v_int32x4>(v_int_arr, "(1.3, 1.4, 1.5, 1.7) after v_floor process: ");   


    //向上取整，处理结果为2 2 2 2
    v_int_arr = cv::v_ceil(v_float_arr);
    PrintVector<cv::v_int32x4>(v_int_arr, "(1.3, 1.4, 1.5, 1.7) after v_ceil process: ");

    //四舍五入取整，处理结果为：1 1 2 2
    v_int_arr = cv::v_round(v_float_arr);
    PrintVector<cv::v_int32x4>(v_int_arr, "(1.3, 1.4, 1.5, 1.7) after v_round process: ");

    //截断小数，目前只实现了保留0为小数，所以结果与v_floor一样
    v_int_arr = cv::v_trunc(v_float_arr);
    PrintVector<cv::v_int32x4>(v_int_arr, "(1.3, 1.4, 1.5, 1.7) after v_trunc process: ");


    //最大最小值
    cv::v_int32x4 v1(1, 5, 2, 6);
    cv::v_int32x4 v2(5, 1, 6, 2);
    cv::v_int32x4 v3 = cv::v_min(v1, v2);//1 1 2 2
    cv::v_int32x4 v4 = cv::v_max(v1, v2);//5 5 6 6 
    int min = cv::v_reduce_min(v1);//1
    int max = cv::v_reduce_max(v2);//6
    PrintVector<cv::v_int32x4>(v1, "v1");
    PrintVector<cv::v_int32x4>(v2, "v2");
    PrintVector<cv::v_int32x4>(v3, "cv::v_min(v1, v2)");
    PrintVector<cv::v_int32x4>(v4, "cv::v_max(v1, v2)");
    std::cout << "cv::v_reduce_min(v1) : " << min << std::endl;
    std::cout << "cv::v_reduce_max(v2) : " << max << std::endl;

    //绝对值|a|
    cv::v_int32x4 v5(-1, 1, -2, 0);
    cv::v_uint32x4 v6 = cv::v_abs(v5);// 1 1 2 0
    PrintVector<cv::v_int32x4>(v5, "v5");
    PrintVector<cv::v_uint32x4>(v6, "cv::v_abs(v5)");
    
    //求取差值的绝对值|a - b|
    cv::v_uint32x4 v7 = cv::v_absdiff(v1, v2);
    PrintVector<cv::v_uint32x4>(v7, "cv::v_absdiff(v1, v2)");

    //求平方根
    cv::v_float32x4 v8(1.0f, 4.0f, 9.0f, 16.0f);
    cv::v_float32x4 v9 = cv::v_sqrt(v8);//数据类型只能为float
    PrintVector(v8, "v8");
    PrintVector(v9, "cv::v_sqrt(v8)");

    //求平方和（幅度值）
    cv::v_float32x4 v10(1.0f, 1.0f, 1.0f, 1.0f);
    cv::v_float32x4 v11(1.0f, 1.0f, 1.0f, 1.0f);
    cv::v_float32x4 v12 = cv::v_magnitude(v10, v11);
    cv::v_float32x4 v13 = cv::v_sqr_magnitude(v10, v11);
    PrintVector(v10, "v10");
    PrintVector(v11, "v11");
    PrintVector(v12, "cv::v_magnitude(v10, v11)");
    PrintVector(v13, "cv::v_sqr_magnitude(v10, v11)");

    //求和
    cv::v_int32x4 v14(1, 2, 3, 4);
    int sum = cv::v_reduce_sum(v14);
    PrintVector(v14, "v14");
    std::cout << "cv::v_reduce_sum(v14) : "<< sum << std::endl;
}
```

### 兼容OpenMP并行

```cpp
//结合openmp并行使用
void OpitmizeWithOpenMP()
{
    std::cout << "----------------------OpenCV Simd with OpenMP sample--------------------------" << std::endl;

    //初始化数组
    const size_t  array_size = 40000000;
    int* int_array_a = new int[array_size];
    int* int_array_b = new int[array_size];
    int* int_array_c = new int[array_size];


    //原循环程序计算
    for (size_t i = 0; i < array_size; i++)
    {
        int_array_c[i] = int_array_a[i] * int_array_b[i];
    }

    //Simd优化
    for (size_t i = 0; i < array_size; i += cv::v_int32x4::nlanes)
    {
        //加载
        cv::v_int32x4 v_a = cv::v_load(int_array_a + i);
        cv::v_int32x4 v_b = cv::v_load(int_array_b + i);
        //计算
        cv::v_int32x4 v_c = v_a * v_b;
        //储存
        cv::v_store(int_array_c + i, v_c);
    }

    //simd + OpenMP优化
    int step = cv::v_int32x4::nlanes;
    #pragma omp parallel for
    for (int i = 0; i < array_size; i += step)
    {
        //加载
        cv::v_int32x4 v_a = cv::v_load(int_array_a + i);
        cv::v_int32x4 v_b = cv::v_load(int_array_b + i);
        //计算
        cv::v_int32x4 v_c = v_a * v_b;
        //储存
        cv::v_store(int_array_c + i, v_c);
    }
}
```

### 条件分支

```cpp
void ConditionSample()
{
    std::cout << "------------------------------Condition sample-----------------------------------" << std::endl;
    const size_t array_size = 400;
    int* int_array_a = new int[array_size];
    int* int_array_b = new int[array_size];
    
    //原程序
    for (size_t i = 0; i < array_size; i++)
    {
        if (int_array_a[i] > 0)
        {
            int_array_b[i] = int_array_a[i];
        }
    }

    //simd优化版本
    for (size_t i = 0; i < array_size; i += 4)
    {
        //加载
        cv::v_int32x4 v_a = cv::v_load(int_array_a + i);
        cv::v_int32x4 v_b = cv::v_load(int_array_b + i);

        //计算:构造条件
        auto condiction_mask = v_a > cv::v_setzero_s32();
        // b = mask ? a : b
        v_b = cv::v_select(condiction_mask, v_a, v_b);

        //存储
        cv::v_store(int_array_b + i, v_b);
    }
    
    //simd优化自适应加速内存位宽版本
    for (size_t i = 0; i < array_size; i += cv::v_int32::nlanes)
    {
        //加载
        cv::v_int32 v_a = cv::vx_load(int_array_a + i);
        cv::v_int32 v_b = cv::vx_load(int_array_b + i);
        //计算:构造条件
        auto condiction_mask = v_a > cv::v_setzero_s32();
        // b = mask ? a : b
        v_b = cv::v_select(condiction_mask, v_a, v_b);
        //存储
        cv::vx_store(int_array_b + i, v_b);
    }
    
    //simd + openmp优化版本
#pragma omp parallel for
    for (int i = 0; i < array_size; i += int(cv::v_int32::nlanes))
    {
        //加载
        cv::v_int32 v_a = cv::vx_load(int_array_a + i);
        cv::v_int32 v_b = cv::vx_load(int_array_b + i);
        //计算:构造条件
        auto condiction_mask = v_a > cv::v_setzero_s32();
        // b = mask ? a : b
        v_b = cv::v_select(condiction_mask, v_a, v_b);
        //存储
        cv::vx_store(int_array_b + i, v_b);
    }
}
```

### 完整代码

```cpp
/**
 * @file main.cpp
 * @author mango (you@domain.com)
 * @brief OpenCV夸平台Simd学习
 * @version 0.1
 * @date 2021-10-15
 * 
 * @copyright Copyright (c) 2021
 * 
 */

#include <iostream>
#include <chrono>
#include <random>

#include "opencv2/opencv.hpp"
#include "opencv2/core/simd_intrinsics.hpp"

#include "omp.h"


//测试系统平台信息输出
void PrintTestPlatform()
{
    std::cout << "------------------------test platform informaintion----------------------------"<<std::endl;

    std::string system_name = 
#if defined(__Apple__)
    "Mac Os"
#elif defined(__linux__)
    "Linux"
#else 
    "Windows";
#endif 
    std::cout<< "System : "<< system_name << std::endl;
    std::string compiler =
#if defined(__clang__)
    "clang " + ver_string(__clang_major__, __clang_minor__, __clang_patchlevel__);
#elif defined(__GNUC__)
    "gcc " + ver_string(__GNUC__, __GNUC_MINOR__, __GNUC_PATCHLEVEL__);
#else
    "msvc " + std::to_string(_MSC_VER);
#endif

    std::cout<< "Compiler : " << compiler << std::endl;
}


//simd信息获取输出
void PrintOpenCVInfo()
{
    std::cout<<"--------------------------OpenCV informaintion--------------------------"<< std::endl;
    //OpenCV版本，Universal Simd在3.4.x版本就开始提供，不过建议使用4.x版本
    std::cout << "OpenCV version:" << cv::getVersionString() << std::endl;
    std::cout << "Simd info: "<< std::endl;
#ifdef CV_SIMD
    //是否支持simd优化
    std::cout << "CV_SIMD : " << CVAUX_STR(CV_SIMD)  << std::endl;
    //simd优化内存位宽
    std::cout << "CV_SIMD_WIDTH : " <<  CVAUX_STR(CV_SIMD_WIDTH) << std::endl;
    //128bit位宽优化是否支持，绝大部分x86架构cpu支持，如常用的sse指令集
    std::cout << "CV_SIMD128 : " <<CVAUX_STR(CV_SIMD128) << std::endl;
    //256bit位宽优化是否支持，大部分近几年的x86架构cpu支持， avxz指令集
    std::cout << "CV_SIMD256: " <<CVAUX_STR(CV_SIMD256) << std::endl;
    //512bit位宽是否支持，这个intel最先搞得，近几年的intel cpu与最新的AMD锐龙支持
    std::cout << "CV_SIMD512 : " CVAUX_STR(CV_SIMD512) << std::endl;
#else
    std::cout << "CV_SIMD is NOT defined." << std::endl;
#endif

#ifdef CV_SIMD
    std::cout << "sizeof(v_uint8) = " << sizeof(cv::v_uint8) << std::endl;
    std::cout << "sizeof(v_int32) = " << sizeof(cv::v_int32) << std::endl;
    std::cout << "sizeof(v_float32) = " << sizeof(cv::v_float32) << std::endl;
#endif
}

//输出向量
template<typename T>
void PrintVector(const T& simd_vector, const std::string& name)
{
    //获取向量数组长度
    int arr_size = int(T::nlanes);
    //申请对应类型c风格数组
    T::lane_type* arr = new T::lane_type[arr_size];
    //将向量装载到c风格数组
    cv::v_store(arr, simd_vector);

    //打印输出
    std::cout << name << " : ";
    for (size_t i = 0; i < arr_size; i++)
    {
        std::cout << arr[i] << " ";
    }   
    std::cout << std::endl;
}

//加载-计算-存储优化步骤
void SimdSample()
{
    std::cout << "----------------------OpenCV Simd simple sample--------------------------" << std::endl;
    
    // 构造一个常量
    cv::v_int32x4 v_c1(1, 1, 1, 1);             //方法一
    cv::v_int32x4 v_c2 = cv::v_setall(1);       //方法二
    cv::v_int32x4 v_c3 = cv::v_setzero_s32();   //方法三，但只能设置0

    // 构造一个simd长度的数组
    cv::v_int32x4 v_a(1, 2, 3, 4);
    cv::v_int32x4 v_b(1, 2, 3, 4);
    //v_a = [ a0, a1, a2, a3]
    //v_b = [ b0, b1, b2, b3]
    //v_c = [a0+b0, a1+b1, a2+b2, a3+b3]
    cv::v_int32x4 v_c = v_a + v_b;
    PrintVector(v_c, "v_c");
    
    //--------------数组循环优化示例--------------------
    const size_t  array_size = 400;
    int* int_array_a = new int[array_size];
    int* int_array_b = new int[array_size];
    int* int_array_c = new int[array_size];

    //原循环程序计算
    for (int i = 0; i < array_size; i++)
    {
        int_array_c[i] = int_array_a[i] * int_array_b[i];
    }

    //Simd优化:已知编译器向量化内存宽度（如常用的sse为128bit的向量化内存位宽）
    for (int i = 0; i < array_size; i += 4)
    {
        //加载
        cv::v_int32x4 v_a = cv::v_load(int_array_a + i);//一次从数组中读取4个int变量存储到v_a
        cv::v_int32x4 v_b = cv::v_load(int_array_b + i);//一次从数组中读取4个int变量存储到v_b
        //计算
        cv::v_int32x4 v_c = v_a * v_b;//一次性计算4个变量乘法
        //v_a = [ a0, a1, a2, a3]
        //v_b = [ b0, b1, b2, b3]
        //v_c = [a0*b0, a1*b1, a2*b2, a3*b3]
        //储存
        cv::v_store(int_array_c + i, v_c);
    }

    //Simd自适位宽优化：未知编译器采用的向量化位宽
    for (size_t i = 0; i < array_size; i += cv::v_int32::nlanes)
    {
        //加载 n = 向量化内存位宽 / sizeof(int),如sse是 128 / 32 = 4, avx2 是256 / 32 = 8
        cv::v_int32 v_a = cv::vx_load(int_array_a + i);//一次从数组中读取n个int变量存储到v_a
        cv::v_int32 v_b = cv::vx_load(int_array_b + i);//一次从数组中读取n个int变量存储到v_b
        //计算
        cv::v_int32 v_c = v_a * v_b;//一次性计算n个变量乘法
        //v_a = [ a0, a1, a2, a3]
        //v_b = [ b0, b1, b2, b3]
        //v_c = [a0*b0, a1*b1, a2*b2, a3*b3]
        //储存
        cv::vx_store(int_array_c + i, v_c);
    }
}

//顺组对齐示例，长度不是向量优化位宽时倍数
void LoopLenghtAligned()
{
    const size_t  array_size = 4007;
    uchar* uchar_array_a = new uchar[array_size];
    uchar* uchar_array_b = new uchar[array_size];
    uchar* uchar_array_c = new uchar[array_size];

    //一次性加载16个uchar数据进行计算，uchar占8bit, 8x16=128bit
    size_t i = 0;
    for (; i < array_size - 1; i += 16)
    {
        //加载
        cv::v_uint8x16 v_a = cv::v_load(uchar_array_a + i);
        cv::v_uint8x16 v_b = cv::v_load(uchar_array_b + i);
        //计算...
        cv::v_uint8x16 v_c = v_a + v_b;

        //存储
        cv::v_store(uchar_array_c + i, v_c);
    }
    for (; i < array_size; i++)
    {
        uchar_array_c[i] = uchar_array_a[i] + uchar_array_b[i];
    }
}

//uchar short int float类型数组优化
void BasicDataArrayOptimization()
{
    std::cout << "----------------------OpenCV Simd simple sample--------------------------" << std::endl;

    //uchar
    const size_t  array_size = 4000;
    uchar* uchar_array_a = new uchar[array_size];
    uchar* uchar_array_b = new uchar[array_size];
    uchar* uchar_array_c = new uchar[array_size];

    //一次性加载16个uchar数据进行计算，uchar占8bit, 8x16=128bit
    for (size_t i = 0; i < array_size; i += 16)
    {
        //加载
        cv::v_uint8x16 v_a = cv::v_load(uchar_array_a + i);
        cv::v_uint8x16 v_b = cv::v_load(uchar_array_b + i);
        //计算...
        cv::v_uint8x16 v_c = v_a + v_b;

        //存储
        cv::v_store(uchar_array_c + i, v_c);
    }

    //short
    short* short_arrary_a = new short[array_size];
    short* short_arrary_b = new short[array_size];
    short* short_arrary_c = new short[array_size];
    
    //一次性加载8个short数据进行计算，short占16bit, 16x8=128bit
    for (size_t i = 0; i < array_size; i += 8)
    {
        //加载
        cv::v_int16x8 v_a = cv::v_load(short_arrary_a + i);
        cv::v_int16x8 v_b = cv::v_load(short_arrary_b + i);
        //计算
        cv::v_int16x8 v_c = v_a + v_b;
        //存储
        cv::v_store(short_arrary_c + i, v_c);
    }


    //float
    float* float_array_a = new float[array_size];
    float* float_array_b = new float[array_size];
    float* float_array_c = new float[array_size];
    
    //一次性加载4个float数据进行计算，float占32bit, 32x4=128bit
    for (size_t i = 0; i < array_size; i += 4)
    {
        //加载
        cv::v_float32x4 v_a = cv::v_load(float_array_a + i);
        cv::v_float32x4 v_b = cv::v_load(float_array_a + i);
        //计算
        cv::v_float32x4 v_c = v_a + v_b;
        //存储
        cv::v_store(float_array_c + i, v_c);
    }
}


//if条件分支
void ConditionSample()
{
    std::cout << "------------------------------Condition sample-----------------------------------" << std::endl;
    const size_t array_size = 400;
    int* int_array_a = new int[array_size];
    int* int_array_b = new int[array_size];
    
    //原程序
    for (size_t i = 0; i < array_size; i++)
    {
        if (int_array_a[i] > 0)
        {
            int_array_b[i] = int_array_a[i];
        }
    }

    //simd优化版本
    for (size_t i = 0; i < array_size; i += 4)
    {
        //加载
        cv::v_int32x4 v_a = cv::v_load(int_array_a + i);
        cv::v_int32x4 v_b = cv::v_load(int_array_b + i);

        //计算:构造条件
        auto condiction_mask = v_a > cv::v_setzero_s32();
        // b = mask ? a : b
        v_b = cv::v_select(condiction_mask, v_a, v_b);

        //存储
        cv::v_store(int_array_b + i, v_b);
    }
    
    //simd优化自适应加速内存位宽版本
    for (size_t i = 0; i < array_size; i += cv::v_int32::nlanes)
    {
        //加载
        cv::v_int32 v_a = cv::vx_load(int_array_a + i);
        cv::v_int32 v_b = cv::vx_load(int_array_b + i);
        //计算:构造条件
        auto condiction_mask = v_a > cv::v_setzero_s32();
        // b = mask ? a : b
        v_b = cv::v_select(condiction_mask, v_a, v_b);
        //存储
        cv::vx_store(int_array_b + i, v_b);
    }
    
    //simd + openmp优化版本
#pragma omp parallel for
    for (int i = 0; i < array_size; i += int(cv::v_int32::nlanes))
    {
        //加载
        cv::v_int32 v_a = cv::vx_load(int_array_a + i);
        cv::v_int32 v_b = cv::vx_load(int_array_b + i);
        //计算:构造条件
        auto condiction_mask = v_a > cv::v_setzero_s32();
        // b = mask ? a : b
        v_b = cv::v_select(condiction_mask, v_a, v_b);
        //存储
        cv::vx_store(int_array_b + i, v_b);
    }
}

//数学函数
void MathematicsFunctionSample()
{
    std::cout << "-----------------------Mathematics function sample------------------------------" << std::endl;
    cv::v_float32 v_float_arr(1.3, 1.4, 1.5, 1.7);

    //向下取整，处理结果为1 1 1 1
    cv::v_int32x4 v_int_arr = cv::v_floor(v_float_arr);
    PrintVector<cv::v_int32x4>(v_int_arr, "(1.3, 1.4, 1.5, 1.7) after v_floor process: ");   


    //向上取整，处理结果为2 2 2 2
    v_int_arr = cv::v_ceil(v_float_arr);
    PrintVector<cv::v_int32x4>(v_int_arr, "(1.3, 1.4, 1.5, 1.7) after v_ceil process: ");

    //四舍五入取整，处理结果为：1 1 2 2
    v_int_arr = cv::v_round(v_float_arr);
    PrintVector<cv::v_int32x4>(v_int_arr, "(1.3, 1.4, 1.5, 1.7) after v_round process: ");

    //截断小数，目前只实现了保留0为小数，所以结果与v_floor一样
    v_int_arr = cv::v_trunc(v_float_arr);
    PrintVector<cv::v_int32x4>(v_int_arr, "(1.3, 1.4, 1.5, 1.7) after v_trunc process: ");


    //最大最小值
    cv::v_int32x4 v1(1, 5, 2, 6);
    cv::v_int32x4 v2(5, 1, 6, 2);
    cv::v_int32x4 v3 = cv::v_min(v1, v2);//1 1 2 2
    cv::v_int32x4 v4 = cv::v_max(v1, v2);//5 5 6 6 
    int min = cv::v_reduce_min(v1);//1
    int max = cv::v_reduce_max(v2);//6
    PrintVector<cv::v_int32x4>(v1, "v1");
    PrintVector<cv::v_int32x4>(v2, "v2");
    PrintVector<cv::v_int32x4>(v3, "cv::v_min(v1, v2)");
    PrintVector<cv::v_int32x4>(v4, "cv::v_max(v1, v2)");
    std::cout << "cv::v_reduce_min(v1) : " << min << std::endl;
    std::cout << "cv::v_reduce_max(v2) : " << max << std::endl;

    //绝对值|a|
    cv::v_int32x4 v5(-1, 1, -2, 0);
    cv::v_uint32x4 v6 = cv::v_abs(v5);// 1 1 2 0
    PrintVector<cv::v_int32x4>(v5, "v5");
    PrintVector<cv::v_uint32x4>(v6, "cv::v_abs(v5)");
    
    //求取差值的绝对值|a - b|
    cv::v_uint32x4 v7 = cv::v_absdiff(v1, v2);
    PrintVector<cv::v_uint32x4>(v7, "cv::v_absdiff(v1, v2)");

    //求平方根
    cv::v_float32x4 v8(1.0f, 4.0f, 9.0f, 16.0f);
    cv::v_float32x4 v9 = cv::v_sqrt(v8);//数据类型只能为float
    PrintVector(v8, "v8");
    PrintVector(v9, "cv::v_sqrt(v8)");

    //求平方和（幅度值）
    cv::v_float32x4 v10(1.0f, 1.0f, 1.0f, 1.0f);
    cv::v_float32x4 v11(1.0f, 1.0f, 1.0f, 1.0f);
    cv::v_float32x4 v12 = cv::v_magnitude(v10, v11);
    cv::v_float32x4 v13 = cv::v_sqr_magnitude(v10, v11);
    PrintVector(v10, "v10");
    PrintVector(v11, "v11");
    PrintVector(v12, "cv::v_magnitude(v10, v11)");
    PrintVector(v13, "cv::v_sqr_magnitude(v10, v11)");

    //求和
    cv::v_int32x4 v14(1, 2, 3, 4);
    int sum = cv::v_reduce_sum(v14);
    PrintVector(v14, "v14");
    std::cout << "cv::v_reduce_sum(v14) : "<< sum << std::endl;
}

//结合openmp并行使用
void OpitmizeWithOpenMP()
{
    std::cout << "----------------------OpenCV Simd with OpenMP sample--------------------------" << std::endl;

    //初始化数组
    const size_t  array_size = 40000000;
    int* int_array_a = new int[array_size];
    int* int_array_b = new int[array_size];
    int* int_array_c = new int[array_size];


    //原循环程序计算
    for (size_t i = 0; i < array_size; i++)
    {
        int_array_c[i] = int_array_a[i] * int_array_b[i];
    }

    //Simd优化
    for (size_t i = 0; i < array_size; i += cv::v_int32x4::nlanes)
    {
        //加载
        cv::v_int32x4 v_a = cv::v_load(int_array_a + i);
        cv::v_int32x4 v_b = cv::v_load(int_array_b + i);
        //计算
        cv::v_int32x4 v_c = v_a * v_b;
        //储存
        cv::v_store(int_array_c + i, v_c);
    }

    //simd + OpenMP优化
    int step = cv::v_int32x4::nlanes;
    #pragma omp parallel for
    for (int i = 0; i < array_size; i += step)
    {
        //加载
        cv::v_int32x4 v_a = cv::v_load(int_array_a + i);
        cv::v_int32x4 v_b = cv::v_load(int_array_b + i);
        //计算
        cv::v_int32x4 v_c = v_a * v_b;
        //储存
        cv::v_store(int_array_c + i, v_c);
    }
}

int main(int argc, char** argv)
{
    //测试平台信息
    PrintTestPlatform();
    //OpenCV信息
    PrintOpenCVInfo();
    //简单使用OpenCV统一simd框架示例
    SimdSample();
    //集成OpenMP并行优化示例
    OpitmizeWithOpenMP();
    //顺组对齐示例，长度不是向量优化位宽时倍数
    LoopLenghtAligned();
    //基础数据类型数组循环优化示例
    BasicDataArrayOptimization();
    //条件分支示例
    ConditionSample();
    //数学函数操作相关示例
    MathematicsFunctionSample();
    return 0;
}
```
----------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/opencv/opencv-universal-intrinsics-simd-1.html