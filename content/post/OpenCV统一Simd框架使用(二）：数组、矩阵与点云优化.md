---
title: "OpenCV统一Simd框架使用(二）：数组、矩阵与点云优化"
categories: [ "opencv","c++","计算机视觉" ]
tags: [ "opencv","openmp","avx","simd","universal-inrinsics","sse","neo","mat","vector","cloud" ]
draft: false
slug: "opencv/opencv-universal-intrinsics-simd-2"
date: "2021-10-21 18:57:00"
---

## 一维数组Simd优化

### c风格数组

用c语言风格神奇如下的一维数组：

```cpp
const size_t count = 10000;

//c风格数组
int* arr_a = new int[count];
int* arr_b = new int[count];
int* arr_c = new int[count];
```
假设数组需要做如下的计算处理，即c = a + b,

```cpp
//源程序
{
    for (size_t i = 0; i < count; i++)
    {
        arr_c[i] = arr_a[i] + arr_b[i];
    }
}
```
运算非常简单，只需要一个循环遍历，单只能每次循环计算一遍。下面使用simd优化，当固定simd向量位宽的时候：

```cpp
//simd固定向量长优化
{        
#ifdef CV_SIMD128
    size_t step = cv::v_int32x4::nlanes;
    size_t i = 0;
    for (; i < count - step; i += step)
    {
        cv::v_int32x4 va = cv::v_load(arr_a + i);
        cv::v_int32x4 vb = cv::v_load(arr_b + i);
        cv::v_int32x4 vc = va + vb;
        cv::v_store(arr_c + i, vc);
    }
    //处理数组尾部未够一个step的数据
    for (; i < count; i++)
    {
        arr_c[i] = arr_a[i] + arr_b[i];
    }
#endif 
}
```
选择自适应simd位宽时：

```cpp
//自适应长度simd优化
{
    size_t step = cv::v_int32::nlanes;
    size_t i = 0;
    for (; i < count - step; i += step)
    {
        cv::v_int32 va = cv::vx_load(arr_a + i);
        cv::v_int32 vb = cv::vx_load(arr_b + i);
        cv::v_int32 vc = va + vb;
        cv::vx_store(arr_c + i, vc);
    }
    //处理数组尾部未够一个step的数据
    for (; i < count; i++)
    {
        arr_c[i] = arr_a[i] + arr_b[i];
    }
}
```
同时，还可以结合openmp的并行多线程优化：
```cpp
{
    //c风格数组优化结合openmp并行
    int step = cv::v_int32::nlanes;
#pragma omp parallel for 
    for (int i = 0; i < count - step; i += step)
    {
        cv::v_int32 va = cv::vx_load(arr_a + i);
        cv::v_int32 vb = cv::vx_load(arr_b + i);
        cv::v_int32 vc = va + vb;
        cv::vx_store(arr_c + i, vc);
    }
    //处理数组尾部未够一个step的数据
    int current_i = count - count % step;
    for (int i = current_i; i < count; i++)
    {
        arr_c[i] = arr_a[i] + arr_b[i];
    }
}
```
### ste::vector数组

同样地，初始化如下数组

```cpp
std::vector<float> arr_a(count, 1.0f);
std::vector<float> arr_b(count, 1.0f);
std::vector<float> arr_c(count, 1.0f);
```
做一样两数组元素相加计算处理：

```cpp
//源程序
{
    for (size_t i = 0; i < count; i++)
    {
        arr_c[i] = arr_a[i] + arr_b[i];
    }
}
```
下面使用Simd优化vector数组，参考c风格数组，处理非常相似。当固定Simd位宽时：
```cpp
//simd固定向量长优化
{
#ifdef CV_SIMD128
    size_t i = 0;
    for (; i < count - 4; i += 4)
    {
        cv::v_float32x4 va = cv::v_load(arr_a.begin()._Ptr + i);    //或者cv::v_float32x4 va(arr_a[i], arr_a[i + 1], arr_a[i + 2], arr_a[i + 3]);
        cv::v_float32x4 vb = cv::v_load(arr_b.begin()._Ptr + i);    //或者cv::v_float32x4 vb(arr_b[i], arr_b[i + 1], arr_b[i + 2], arr_b[i + 3]);
        cv::v_float32x4 vc = va + vb;
        cv::v_store(arr_c.begin()._Ptr + i, vc);
    }
    //处理数组尾部未够一个step的数据
    for (; i < count; i++)
    {
        arr_c[i] = arr_a[i] + arr_b[i];
    }
#endif 
}
```
自适应位宽simd优化如下：
```cpp
//自适应长度simd优化
{
    size_t i = 0;
    size_t step = cv::v_float32::nlanes;
    float* temp_arr = new float[step];
    for (; i < count - step; i += step)
    {
        cv::v_float32 va = cv::vx_load(arr_a.begin()._Ptr + i);
        cv::v_float32 vb = cv::vx_load(arr_b.begin()._Ptr + i);
        cv::v_float32 vc = va + vb;
        cv::vx_store(arr_c.begin()._Ptr + i, vc);
    }
    //处理数组尾部未够一个step的数据
    for (; i < count; i++)
    {
        arr_c[i] = arr_a[i] + arr_b[i];
    }
}
```

依然可以兼容openmp多线程并行

```cpp
//std::vector动态数组优化结合openmp并行
{
    int step = cv::v_float32::nlanes;
    float* temp_arr = new float[step];
#pragma omp parallel for
    for (int i  = 0; i < count - step; i += step)
    {
        cv::v_float32 va = cv::vx_load(arr_a.begin()._Ptr + i);
        cv::v_float32 vb = cv::vx_load(arr_b.begin()._Ptr + i);
        cv::v_float32 vc = va + vb;
        cv::vx_store(arr_c.begin()._Ptr + i, vc);
    }
    //处理数组尾部未够一个step的数据
    int current_i = count - count % step;
    for (int i = current_i; i < count; i++)
    {
        arr_c[i] = arr_a[i] + arr_b[i];
    }
}
```

示例代码

```cpp
/**
 * @brief 一维数组simd优化示例
 * 
 */
void ArraySimd()
{
    const size_t count = 10000;
    //c风格数组优化
    {
        //c风格数组
        int* arr_a = new int[count];
        int* arr_b = new int[count];
        int* arr_c = new int[count];

        //源程序
        {
            for (size_t i = 0; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
        }
        //simd固定向量长优化
        {        
#ifdef CV_SIMD128
            size_t step = cv::v_int32x4::nlanes;
            size_t i = 0;
            for (; i < count - step; i += step)
            {
                cv::v_int32x4 va = cv::v_load(arr_a + i);
                cv::v_int32x4 vb = cv::v_load(arr_b + i);
                cv::v_int32x4 vc = va + vb;
                cv::v_store(arr_c + i, vc);
            }
            //处理数组尾部未够一个step的数据
            for (; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
#endif 
        }
        //自适应长度simd优化
        {
            size_t step = cv::v_int32::nlanes;
            size_t i = 0;
            for (; i < count - step; i += step)
            {
                cv::v_int32 va = cv::vx_load(arr_a + i);
                cv::v_int32 vb = cv::vx_load(arr_b + i);
                cv::v_int32 vc = va + vb;
                cv::vx_store(arr_c + i, vc);
            }
            //处理数组尾部未够一个step的数据
            for (; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
        }
        {
            //c风格数组优化结合openmp并行
            int step = cv::v_int32::nlanes;
#pragma omp parallel for 
            for (int i = 0; i < count - step; i += step)
            {
                cv::v_int32 va = cv::vx_load(arr_a + i);
                cv::v_int32 vb = cv::vx_load(arr_b + i);
                cv::v_int32 vc = va + vb;
                cv::vx_store(arr_c + i, vc);
            }
            //处理数组尾部未够一个step的数据
            int current_i = count - count % step;
            for (int i = current_i; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
        }
    }

    //std::vector动态数组优化
    {
        std::vector<float> arr_a(count, 1.0f);
        std::vector<float> arr_b(count, 1.0f);
        std::vector<float> arr_c(count, 1.0f);

        //源程序
        {
            for (size_t i = 0; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
        }
        //simd固定向量长优化
        {
#ifdef CV_SIMD128
            size_t i = 0;
            for (; i < count - 4; i += 4)
            {
                cv::v_float32x4 va = cv::v_load(arr_a.begin()._Ptr + i);    //或者cv::v_float32x4 va(arr_a[i], arr_a[i + 1], arr_a[i + 2], arr_a[i + 3]);
                cv::v_float32x4 vb = cv::v_load(arr_b.begin()._Ptr + i);    //或者cv::v_float32x4 vb(arr_b[i], arr_b[i + 1], arr_b[i + 2], arr_b[i + 3]);
                cv::v_float32x4 vc = va + vb;
                cv::v_store(arr_c.begin()._Ptr + i, vc);
            }
            //处理数组尾部未够一个step的数据
            for (; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
#endif 
        }
        //自适应长度simd优化
        {
            size_t i = 0;
            size_t step = cv::v_float32::nlanes;
            float* temp_arr = new float[step];
            for (; i < count - step; i += step)
            {
                cv::v_float32 va = cv::vx_load(arr_a.begin()._Ptr + i);
                cv::v_float32 vb = cv::vx_load(arr_b.begin()._Ptr + i);
                cv::v_float32 vc = va + vb;
                cv::vx_store(arr_c.begin()._Ptr + i, vc);
            }
            //处理数组尾部未够一个step的数据
            for (; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
        }
        //std::vector动态数组优化结合openmp并行
        {
            int step = cv::v_float32::nlanes;
            float* temp_arr = new float[step];
#pragma omp parallel for
            for (int i  = 0; i < count - step; i += step)
            {
                cv::v_float32 va = cv::vx_load(arr_a.begin()._Ptr + i);
                cv::v_float32 vb = cv::vx_load(arr_b.begin()._Ptr + i);
                cv::v_float32 vc = va + vb;
                cv::vx_store(arr_c.begin()._Ptr + i, vc);
            }
            //处理数组尾部未够一个step的数据
            int current_i = count - count % step;
            for (int i = current_i; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
        }
    }
}
```
## 二维矩阵Simd优化

下面介绍二维矩阵数据的Simd优化，以OpenCV的主要数据结构Mat为例。首先构造一个Mat对象，并初始化数据元素为0-255之间的随机数，得到一副随机的灰度图。
```cpp
const size_t mat_size = 1000;
cv::Mat gray_image = cv::Mat(mat_size, mat_size, CV_8UC1);
cv::Mat binary_image = cv::Mat(mat_size, mat_size, CV_8UC1);
std::random_device rd;
std::mt19937 gen(rd());
std::uniform_int_distribution<>  dist(0, 255);
const uchar threshold = 200;
for (size_t i = 0; i < mat_size; i++)
{
    uchar* pg = gray_image.ptr<uchar>(i);
    for (size_t j = 0; j < mat_size; j++)
    {
        pg[j] = dist(gen);
    }
}
```

以最常见的阈值二值化为例，将构造的随机灰度图，二值化。未经优化的二值化处理程序如下，代码非常简单，遍历每个像素与阈值对比后置为0或者255即可。

```cpp
//源程序:二值化图像
{
    for (size_t i = 0; i < mat_size; i++)
    {
        uchar* pg = gray_image.ptr<uchar>(i);
        uchar* pb = binary_image.ptr<uchar>(i);
        for (size_t j = 0; j < mat_size; j++)
        {
            pb[j] = pg[j] > threshold ? 255 : 0;
        }
    }
}
```
固定向量长优化

```cpp
//simd固定向量长优化
{
#ifdef CV_SIMD128
    size_t step = cv::v_uint8x16::nlanes;//4
    cv::v_uint8x16 v_threshold = cv::v_setall(threshold);
    cv::v_uint8x16 v_255 = cv::v_setall(uchar(255));
    cv::v_uint8x16 v_0 = cv::v_setall(uchar(0));
    for (size_t i = 0; i < mat_size; i++)
    {
        uchar* pg = gray_image.ptr<uchar>(i);
        uchar* pb = binary_image.ptr<uchar>(i);
        
        for (size_t j = 0; j < mat_size - step; j += step)
        {
            cv::v_uint8x16 vg = cv::v_load(pg + j);
            auto condition = vg > v_threshold;
            cv::v_uint8x16 vb = cv::v_select(condition,v_255 , v_0);
            cv::v_store(pb + j, vb);
        }
        //处理一行尾部未够一个step的像素
        size_t current_j = mat_size - mat_size % step;
        for (size_t j = current_j; j < mat_size; j ++)
        {
            pb[j] = pg[j] > threshold ? 255 : 0;
        }
    }
#endif
}
```

自适应长度Simd:

```cpp
//自适应长度simd优化
{
    size_t step = cv::v_uint8::nlanes;
    cv::v_uint8 v_threshold = cv::vx_setall(threshold);
    cv::v_uint8 v_255 = cv::vx_setall(uchar(255));
    cv::v_uint8 v_0 = cv::vx_setall(uchar(0));
    for (size_t i = 0; i < mat_size; i++)
    {
        uchar* pg = gray_image.ptr<uchar>(i);
        uchar* pb = binary_image.ptr<uchar>(i);

        for (size_t j = 0; j < mat_size - step; j += step)
        {
            cv::v_uint8 vg = cv::vx_load(pg + j);
            auto condition = vg > v_threshold;
            cv::v_uint8 vb = cv::v_select(condition, v_255, v_0);
            cv::vx_store(pb + j, vb);
        }
        //处理一行尾部未够一个step的像素
        size_t current_j = mat_size - mat_size % step;
        for (size_t j = current_j; j < mat_size; j++)
        {
            pb[j] = pg[j] > threshold ? 255 : 0;
        }
    }
}
```
与OpenMP结合多线程优化：

```cpp
//优化结合openmp并行
{
    size_t step = cv::v_uint8::nlanes;
    cv::v_uint8 v_threshold = cv::vx_setall(threshold);
    cv::v_uint8 v_255 = cv::vx_setall(uchar(255));
    cv::v_uint8 v_0 = cv::vx_setall(uchar(0));
    //一般双循环openmp并行用于优化外循环
#pragma omp parallel for
    for (int i = 0; i < mat_size; i++)
    {
        uchar* pg = gray_image.ptr<uchar>(i);
        uchar* pb = binary_image.ptr<uchar>(i);

        for (size_t j = 0; j < mat_size - step; j += step)
        {
            cv::v_uint8 vg = cv::vx_load(pg + j);
            auto condition = vg > v_threshold;
            cv::v_uint8 vb = cv::v_select(condition, v_255, v_0);
            cv::vx_store(pb + j, vb);
        }
        //处理一行尾部未够一个step的像素
        size_t current_j = mat_size - mat_size % step;
        for (size_t j = current_j; j < mat_size; j++)
        {
            pb[j] = pg[j] > threshold ? 255 : 0;
        }
    }
}
```

本质上，Mat底层数据也是一维数组数据，如果能把双层循环展开降低为一层循环，那可以做进一步优化。OpenCV的Mat可以展开为单循环遍历像素点，但是需要先检查底层数据是否为连续的：
```cpp
//展开为一维数组优化
{
    size_t step = cv::v_uint8::nlanes;
    cv::v_uint8 v_threshold = cv::vx_setall(threshold);
    cv::v_uint8 v_255 = cv::vx_setall(uchar(255));
    cv::v_uint8 v_0 = cv::vx_setall(uchar(0));

    //cv::Mat需要判断内存是否为连续分配的才可以展开
    if(gray_image.isContinuous() && binary_image.isContinuous())
    {
        uchar* pg = gray_image.ptr<uchar>(0);
        uchar* pb = binary_image.ptr<uchar>(0);
        //展开为单循环
        int len = gray_image.rows * gray_image.cols;
#pragma omp parallel for
        for (int j = 0; j < len - step; j += step)
        {
            cv::v_uint8 vg = cv::vx_load(pg + j);
            auto condition = vg > v_threshold;
            cv::v_uint8 vb = cv::v_select(condition, v_255, v_0);
            cv::vx_store(pb + j, vb);
        }
        //处理一行尾部未够一个step的像素
        size_t current_j = len - len % step;
        for (size_t j = current_j; j < len; j++)
        {
            pb[j] = pg[j] > threshold ? 255 : 0;
        }
    }
}
```

二维矩阵优化示例代码：

```cpp
void MatSimd()
{
    const size_t mat_size = 1000;
    cv::Mat gray_image = cv::Mat(mat_size, mat_size, CV_8UC1);
    cv::Mat binary_image = cv::Mat(mat_size, mat_size, CV_8UC1);
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_int_distribution<>  dist(0, 255);
    const uchar threshold = 200;
    for (size_t i = 0; i < mat_size; i++)
    {
        uchar* pg = gray_image.ptr<uchar>(i);
        for (size_t j = 0; j < mat_size; j++)
        {
            pg[j] = dist(gen);
        }
    }
    
    //源程序:二值化图像
    {
        for (size_t i = 0; i < mat_size; i++)
        {
            uchar* pg = gray_image.ptr<uchar>(i);
            uchar* pb = binary_image.ptr<uchar>(i);
            for (size_t j = 0; j < mat_size; j++)
            {
                pb[j] = pg[j] > threshold ? 255 : 0;
            }
        }
    }
    //simd固定向量长优化
    {
#ifdef CV_SIMD128
        size_t step = cv::v_uint8x16::nlanes;//4
        cv::v_uint8x16 v_threshold = cv::v_setall(threshold);
        cv::v_uint8x16 v_255 = cv::v_setall(uchar(255));
        cv::v_uint8x16 v_0 = cv::v_setall(uchar(0));
        for (size_t i = 0; i < mat_size; i++)
        {
            uchar* pg = gray_image.ptr<uchar>(i);
            uchar* pb = binary_image.ptr<uchar>(i);
            
            for (size_t j = 0; j < mat_size - step; j += step)
            {
                cv::v_uint8x16 vg = cv::v_load(pg + j);
                auto condition = vg > v_threshold;
                cv::v_uint8x16 vb = cv::v_select(condition,v_255 , v_0);
                cv::v_store(pb + j, vb);
            }
            //处理一行尾部未够一个step的像素
            size_t current_j = mat_size - mat_size % step;
            for (size_t j = current_j; j < mat_size; j ++)
            {
                pb[j] = pg[j] > threshold ? 255 : 0;
            }
        }
#endif
    }
    //自适应长度simd优化
    {
        size_t step = cv::v_uint8::nlanes;
        cv::v_uint8 v_threshold = cv::vx_setall(threshold);
        cv::v_uint8 v_255 = cv::vx_setall(uchar(255));
        cv::v_uint8 v_0 = cv::vx_setall(uchar(0));
        for (size_t i = 0; i < mat_size; i++)
        {
            uchar* pg = gray_image.ptr<uchar>(i);
            uchar* pb = binary_image.ptr<uchar>(i);

            for (size_t j = 0; j < mat_size - step; j += step)
            {
                cv::v_uint8 vg = cv::vx_load(pg + j);
                auto condition = vg > v_threshold;
                cv::v_uint8 vb = cv::v_select(condition, v_255, v_0);
                cv::vx_store(pb + j, vb);
            }
            //处理一行尾部未够一个step的像素
            size_t current_j = mat_size - mat_size % step;
            for (size_t j = current_j; j < mat_size; j++)
            {
                pb[j] = pg[j] > threshold ? 255 : 0;
            }
        }
    }
    //优化结合openmp并行
    {
        size_t step = cv::v_uint8::nlanes;
        cv::v_uint8 v_threshold = cv::vx_setall(threshold);
        cv::v_uint8 v_255 = cv::vx_setall(uchar(255));
        cv::v_uint8 v_0 = cv::vx_setall(uchar(0));
        //一般双循环openmp并行用于优化外循环
#pragma omp parallel for
        for (int i = 0; i < mat_size; i++)
        {
            uchar* pg = gray_image.ptr<uchar>(i);
            uchar* pb = binary_image.ptr<uchar>(i);

            for (size_t j = 0; j < mat_size - step; j += step)
            {
                cv::v_uint8 vg = cv::vx_load(pg + j);
                auto condition = vg > v_threshold;
                cv::v_uint8 vb = cv::v_select(condition, v_255, v_0);
                cv::vx_store(pb + j, vb);
            }
            //处理一行尾部未够一个step的像素
            size_t current_j = mat_size - mat_size % step;
            for (size_t j = current_j; j < mat_size; j++)
            {
                pb[j] = pg[j] > threshold ? 255 : 0;
            }
        }
    }
    //展开为一维数组优化
    {
        size_t step = cv::v_uint8::nlanes;
        cv::v_uint8 v_threshold = cv::vx_setall(threshold);
        cv::v_uint8 v_255 = cv::vx_setall(uchar(255));
        cv::v_uint8 v_0 = cv::vx_setall(uchar(0));

        //cv::Mat需要判断内存是否为连续分配的才可以展开
        if(gray_image.isContinuous() && binary_image.isContinuous())
        {
            uchar* pg = gray_image.ptr<uchar>(0);
            uchar* pb = binary_image.ptr<uchar>(0);
            //展开为单循环
            int len = gray_image.rows * gray_image.cols;
#pragma omp parallel for
            for (int j = 0; j < len - step; j += step)
            {
                cv::v_uint8 vg = cv::vx_load(pg + j);
                auto condition = vg > v_threshold;
                cv::v_uint8 vb = cv::v_select(condition, v_255, v_0);
                cv::vx_store(pb + j, vb);
            }
            //处理一行尾部未够一个step的像素
            size_t current_j = len - len % step;
            for (size_t j = current_j; j < len; j++)
            {
                pb[j] = pg[j] > threshold ? 255 : 0;
            }
        }
    }
}
```

## 三维点云Simd优化

点云的Simd优化就稍微麻烦一点，因为点云有xyz三维数据坐标点集成，底层数据并非连续的，通常都是一个结构体表示坐标点，结构体数组构成点云。首先定义空间坐标点结构体, 以及点云

```cpp
//点结构体
struct Point
{
    float x;
    float y;
    float z;
    Point(): x(1.0f),y(2.0f),z(3.f){}
    Point(const float& x_, const float& y_, const float& z_): x(x_),y(y_),z(z_){}
};

const size_t count = 400;
std::vector<Point> cloud(count);
```
以计算点云的质心为例，未作优化的源程序如下：

```cpp
//源程序
{
    //计算质心
    Point centroid(0,0,0);
    for (size_t i = 0; i < count; i++)
    {
        centroid.x += cloud[i].x;
        centroid.y += cloud[i].y;
        centroid.z += cloud[i].z;
    }
    centroid.x /= count;
    centroid.y /= count;
    centroid.z /= count;

    std::cout << "unoptimization centroid: (" << centroid.x << "," << centroid.y << "," << centroid.z << ")\n";
}
```
固定位宽的Simd优化：

```cpp
//simd固定向量长优化
{
#ifdef  CV_SIMD128
    cv::v_float32x4 centroidx(0, 0, 0, 0);
    cv::v_float32x4 centroidy(0, 0, 0, 0);
    cv::v_float32x4 centroidz(0, 0, 0, 0);
    for (size_t i = 0; i < count; i += 4)
    {
        //x
        cv::v_float32x4 vax(cloud[i].x, cloud[i + 1].x, cloud[i + 2].x, cloud[i + 3].x);
        //y
        cv::v_float32x4 vay(cloud[i].y, cloud[i + 1].y, cloud[i + 2].y, cloud[i + 3].y);
        //z
        cv::v_float32x4 vaz(cloud[i].z, cloud[i + 1].z, cloud[i + 2].z, cloud[i + 3].z);
        centroidx += vax;
        centroidy += vay;
        centroidz += vaz;
    }
    Point centroid(0, 0, 0);
    centroid.x = cv::v_reduce_sum(centroidx);
    centroid.y = cv::v_reduce_sum(centroidy);
    centroid.z = cv::v_reduce_sum(centroidz);
    size_t current_i = count - count % 4;
    for (size_t i = current_i; i < count; i++)
    {
        centroid.x += cloud[i].x;
        centroid.y += cloud[i].y;
        centroid.y += cloud[i].y;
    }
    centroid.x /= count;
    centroid.y /= count;
    centroid.z /= count;

    std::cout << "128bit simd centroid: (" << centroid.x << "," << centroid.y << "," << centroid.z << ")\n";

#endif //  CV_SIMD128

}
```

自适应位宽的Simd优化：

```cpp
//自适应长度simd优化
{
    cv::v_float32 centroidx = cv::vx_setzero_f32();
    cv::v_float32 centroidy = cv::vx_setzero_f32();
    cv::v_float32 centroidz = cv::vx_setzero_f32();
    int step = cv::v_float32::nlanes;
    for (int i = 0; i < count; i += step)
    {
        cv::v_float32 vax = cv::vx_setzero_f32();
        cv::v_float32 vay = cv::vx_setzero_f32();
        cv::v_float32 vaz = cv::vx_setzero_f32();
        //Point内存布局为{x, y, z}
        //Cloud内存布局则为{x1,y1,z1,x2,y2,z2,...,xn,yn,zn)
        //{x1,y1,z1,x2,y2,z2,...,xn,yn,zn) => v1{x1,x2,...,xn}, v2{y1,y2...yn}, v3{z1,z2,...,zn}
        cv::v_load_deinterleave(&(cloud.begin()._Ptr + i)->x, vax, vay, vaz);
        centroidx += vax;
        centroidy += vay;
        centroidz += vaz;
    }
    Point centroid(0, 0, 0);
    centroid.x = cv::v_reduce_sum(centroidx);
    centroid.y = cv::v_reduce_sum(centroidy);
    centroid.z = cv::v_reduce_sum(centroidz);
    size_t current_i = count - count % 4;
    for (size_t i = current_i; i < count; i++)
    {
        centroid.x += cloud[i].x;
        centroid.y += cloud[i].y;
        centroid.y += cloud[i].y;
    }
    centroid.x /= count;
    centroid.y /= count;
    centroid.z /= count;

    std::cout << "auto simd width centroid: (" << centroid.x << "," << centroid.y << "," << centroid.z << ")\n";
}
```
结合OpenMP多线程并行:

```cpp
//自适应长度simd优化结合OpenMP
{
    cv::v_float32 centroidx = cv::vx_setzero_f32();
    cv::v_float32 centroidy = cv::vx_setzero_f32();
    cv::v_float32 centroidz = cv::vx_setzero_f32();
    int step = cv::v_float32::nlanes;
#pragma omp parallel for
    for (int i = 0; i < count; i += step)
    {
        cv::v_float32 vax = cv::vx_setzero_f32();
        cv::v_float32 vay = cv::vx_setzero_f32();
        cv::v_float32 vaz = cv::vx_setzero_f32();
        //Point内存布局为{x, y, z}
        //Cloud内存布局则为{x1,y1,z1,x2,y2,z2,...,xn,yn,zn)
        //{x1,y1,z1,x2,y2,z2,...,xn,yn,zn) => v1{x1,x2,...,xn}, v2{y1,y2...yn}, v3{z1,z2,...,zn}
        cv::v_load_deinterleave(&(cloud.begin()._Ptr + i)->x, vax, vay, vaz);
        centroidx += vax;
        centroidy += vay;
        centroidz += vaz;
    }
    Point centroid(0, 0, 0);
    centroid.x = cv::v_reduce_sum(centroidx);
    centroid.y = cv::v_reduce_sum(centroidy);
    centroid.z = cv::v_reduce_sum(centroidz);
    size_t current_i = count - count % 4;
    for (size_t i = current_i; i < count; i++)
    {
        centroid.x += cloud[i].x;
        centroid.y += cloud[i].y;
        centroid.y += cloud[i].y;
    }
    centroid.x /= count;
    centroid.y /= count;
    centroid.z /= count;

    std::cout << "simd&openmp centroid: (" << centroid.x << "," << centroid.y << "," << centroid.z << ")\n";
}
```

完整的三维点云优化代码示例

```cpp
void PointCloudSimd()
{
    //点结构体
    struct Point
    {
        float x;
        float y;
        float z;
        Point(): x(1.0f),y(2.0f),z(3.f){}
        Point(const float& x_, const float& y_, const float& z_): x(x_),y(y_),z(z_){}
    };
    const size_t count = 400;
    std::vector<Point> cloud(count);

    //源程序
    {
        //计算质心
        Point centroid(0,0,0);
        for (size_t i = 0; i < count; i++)
        {
            centroid.x += cloud[i].x;
            centroid.y += cloud[i].y;
            centroid.z += cloud[i].z;
        }
        centroid.x /= count;
        centroid.y /= count;
        centroid.z /= count;

        std::cout << "unoptimization centroid: (" << centroid.x << "," << centroid.y << "," << centroid.z << ")\n";
    }
    //simd固定向量长优化
    {
#ifdef  CV_SIMD128
        cv::v_float32x4 centroidx(0, 0, 0, 0);
        cv::v_float32x4 centroidy(0, 0, 0, 0);
        cv::v_float32x4 centroidz(0, 0, 0, 0);
        for (size_t i = 0; i < count; i += 4)
        {
            //x
            cv::v_float32x4 vax(cloud[i].x, cloud[i + 1].x, cloud[i + 2].x, cloud[i + 3].x);
            //y
            cv::v_float32x4 vay(cloud[i].y, cloud[i + 1].y, cloud[i + 2].y, cloud[i + 3].y);
            //z
            cv::v_float32x4 vaz(cloud[i].z, cloud[i + 1].z, cloud[i + 2].z, cloud[i + 3].z);
            centroidx += vax;
            centroidy += vay;
            centroidz += vaz;
        }
        Point centroid(0, 0, 0);
        centroid.x = cv::v_reduce_sum(centroidx);
        centroid.y = cv::v_reduce_sum(centroidy);
        centroid.z = cv::v_reduce_sum(centroidz);
        size_t current_i = count - count % 4;
        for (size_t i = current_i; i < count; i++)
        {
            centroid.x += cloud[i].x;
            centroid.y += cloud[i].y;
            centroid.y += cloud[i].y;
        }
        centroid.x /= count;
        centroid.y /= count;
        centroid.z /= count;

        std::cout << "128bit simd centroid: (" << centroid.x << "," << centroid.y << "," << centroid.z << ")\n";

#endif //  CV_SIMD128

    }
    //自适应长度simd优化
    {
        cv::v_float32 centroidx = cv::vx_setzero_f32();
        cv::v_float32 centroidy = cv::vx_setzero_f32();
        cv::v_float32 centroidz = cv::vx_setzero_f32();
        int step = cv::v_float32::nlanes;
        for (int i = 0; i < count; i += step)
        {
            cv::v_float32 vax = cv::vx_setzero_f32();
            cv::v_float32 vay = cv::vx_setzero_f32();
            cv::v_float32 vaz = cv::vx_setzero_f32();
            //Point内存布局为{x, y, z}
            //Cloud内存布局则为{x1,y1,z1,x2,y2,z2,...,xn,yn,zn)
            //{x1,y1,z1,x2,y2,z2,...,xn,yn,zn) => v1{x1,x2,...,xn}, v2{y1,y2...yn}, v3{z1,z2,...,zn}
            cv::v_load_deinterleave(&(cloud.begin()._Ptr + i)->x, vax, vay, vaz);
            centroidx += vax;
            centroidy += vay;
            centroidz += vaz;
        }
        Point centroid(0, 0, 0);
        centroid.x = cv::v_reduce_sum(centroidx);
        centroid.y = cv::v_reduce_sum(centroidy);
        centroid.z = cv::v_reduce_sum(centroidz);
        size_t current_i = count - count % 4;
        for (size_t i = current_i; i < count; i++)
        {
            centroid.x += cloud[i].x;
            centroid.y += cloud[i].y;
            centroid.y += cloud[i].y;
        }
        centroid.x /= count;
        centroid.y /= count;
        centroid.z /= count;

        std::cout << "auto simd width centroid: (" << centroid.x << "," << centroid.y << "," << centroid.z << ")\n";
    }

    //自适应长度simd优化结合OpenMP
    {
        cv::v_float32 centroidx = cv::vx_setzero_f32();
        cv::v_float32 centroidy = cv::vx_setzero_f32();
        cv::v_float32 centroidz = cv::vx_setzero_f32();
        int step = cv::v_float32::nlanes;
#pragma omp parallel for
        for (int i = 0; i < count; i += step)
        {
            cv::v_float32 vax = cv::vx_setzero_f32();
            cv::v_float32 vay = cv::vx_setzero_f32();
            cv::v_float32 vaz = cv::vx_setzero_f32();
            //Point内存布局为{x, y, z}
            //Cloud内存布局则为{x1,y1,z1,x2,y2,z2,...,xn,yn,zn)
            //{x1,y1,z1,x2,y2,z2,...,xn,yn,zn) => v1{x1,x2,...,xn}, v2{y1,y2...yn}, v3{z1,z2,...,zn}
            cv::v_load_deinterleave(&(cloud.begin()._Ptr + i)->x, vax, vay, vaz);
            centroidx += vax;
            centroidy += vay;
            centroidz += vaz;
        }
        Point centroid(0, 0, 0);
        centroid.x = cv::v_reduce_sum(centroidx);
        centroid.y = cv::v_reduce_sum(centroidy);
        centroid.z = cv::v_reduce_sum(centroidz);
        size_t current_i = count - count % 4;
        for (size_t i = current_i; i < count; i++)
        {
            centroid.x += cloud[i].x;
            centroid.y += cloud[i].y;
            centroid.y += cloud[i].y;
        }
        centroid.x /= count;
        centroid.y /= count;
        centroid.z /= count;

        std::cout << "simd&openmp centroid: (" << centroid.x << "," << centroid.y << "," << centroid.z << ")\n";
    }
}
```

运行结果:

```
unoptimization centroid: (1,2,3)
128bit simd centroid: (1,2,3)
auto simd width centroid: (1,2,3)
simd&openmp centroid: (1,2,3)
```
可以看到，优化并未对结果精度造成影响。

## 完整程序代码

```cpp
/**
 * @file example1.cpp
 * @author your name (you@domain.com)
 * @brief 
 * @version 0.1
 * @date 2021-10-21
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

/**
 * @brief 一维数组simd优化示例
 * 
 */
void ArraySimd()
{
    const size_t count = 10000;
    //c风格数组优化
    {
        //c风格数组
        int* arr_a = new int[count];
        int* arr_b = new int[count];
        int* arr_c = new int[count];

        //源程序
        {
            for (size_t i = 0; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
        }
        //simd固定向量长优化
        {        
#ifdef CV_SIMD128
            size_t step = cv::v_int32x4::nlanes;
            size_t i = 0;
            for (; i < count - step; i += step)
            {
                cv::v_int32x4 va = cv::v_load(arr_a + i);
                cv::v_int32x4 vb = cv::v_load(arr_b + i);
                cv::v_int32x4 vc = va + vb;
                cv::v_store(arr_c + i, vc);
            }
            //处理数组尾部未够一个step的数据
            for (; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
#endif 
        }
        //自适应长度simd优化
        {
            size_t step = cv::v_int32::nlanes;
            size_t i = 0;
            for (; i < count - step; i += step)
            {
                cv::v_int32 va = cv::vx_load(arr_a + i);
                cv::v_int32 vb = cv::vx_load(arr_b + i);
                cv::v_int32 vc = va + vb;
                cv::vx_store(arr_c + i, vc);
            }
            //处理数组尾部未够一个step的数据
            for (; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
        }
        {
            //c风格数组优化结合openmp并行
            int step = cv::v_int32::nlanes;
#pragma omp parallel for 
            for (int i = 0; i < count - step; i += step)
            {
                cv::v_int32 va = cv::vx_load(arr_a + i);
                cv::v_int32 vb = cv::vx_load(arr_b + i);
                cv::v_int32 vc = va + vb;
                cv::vx_store(arr_c + i, vc);
            }
            //处理数组尾部未够一个step的数据
            int current_i = count - count % step;
            for (int i = current_i; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
        }
    }

    //std::vector动态数组优化
    {
        std::vector<float> arr_a(count, 1.0f);
        std::vector<float> arr_b(count, 1.0f);
        std::vector<float> arr_c(count, 1.0f);

        //源程序
        {
            for (size_t i = 0; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
        }
        //simd固定向量长优化
        {
#ifdef CV_SIMD128
            size_t i = 0;
            for (; i < count - 4; i += 4)
            {
                cv::v_float32x4 va = cv::v_load(arr_a.begin()._Ptr + i);    //或者cv::v_float32x4 va(arr_a[i], arr_a[i + 1], arr_a[i + 2], arr_a[i + 3]);
                cv::v_float32x4 vb = cv::v_load(arr_b.begin()._Ptr + i);    //或者cv::v_float32x4 vb(arr_b[i], arr_b[i + 1], arr_b[i + 2], arr_b[i + 3]);
                cv::v_float32x4 vc = va + vb;
                cv::v_store(arr_c.begin()._Ptr + i, vc);
            }
            //处理数组尾部未够一个step的数据
            for (; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
#endif 
        }
        //自适应长度simd优化
        {
            size_t i = 0;
            size_t step = cv::v_float32::nlanes;
            float* temp_arr = new float[step];
            for (; i < count - step; i += step)
            {
                cv::v_float32 va = cv::vx_load(arr_a.begin()._Ptr + i);
                cv::v_float32 vb = cv::vx_load(arr_b.begin()._Ptr + i);
                cv::v_float32 vc = va + vb;
                cv::vx_store(arr_c.begin()._Ptr + i, vc);
            }
            //处理数组尾部未够一个step的数据
            for (; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
        }
        //std::vector动态数组优化结合openmp并行
        {
            int step = cv::v_float32::nlanes;
            float* temp_arr = new float[step];
#pragma omp parallel for
            for (int i  = 0; i < count - step; i += step)
            {
                cv::v_float32 va = cv::vx_load(arr_a.begin()._Ptr + i);
                cv::v_float32 vb = cv::vx_load(arr_b.begin()._Ptr + i);
                cv::v_float32 vc = va + vb;
                cv::vx_store(arr_c.begin()._Ptr + i, vc);
            }
            //处理数组尾部未够一个step的数据
            int current_i = count - count % step;
            for (int i = current_i; i < count; i++)
            {
                arr_c[i] = arr_a[i] + arr_b[i];
            }
        }
    }
}

void MatSimd()
{
    const size_t mat_size = 1000;
    cv::Mat gray_image = cv::Mat(mat_size, mat_size, CV_8UC1);
    cv::Mat binary_image = cv::Mat(mat_size, mat_size, CV_8UC1);
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_int_distribution<>  dist(0, 255);
    const uchar threshold = 200;
    for (size_t i = 0; i < mat_size; i++)
    {
        uchar* pg = gray_image.ptr<uchar>(i);
        for (size_t j = 0; j < mat_size; j++)
        {
            pg[j] = dist(gen);
        }
    }
    
    //源程序:二值化图像
    {
        for (size_t i = 0; i < mat_size; i++)
        {
            uchar* pg = gray_image.ptr<uchar>(i);
            uchar* pb = binary_image.ptr<uchar>(i);
            for (size_t j = 0; j < mat_size; j++)
            {
                pb[j] = pg[j] > threshold ? 255 : 0;
            }
        }
    }
    //simd固定向量长优化
    {
#ifdef CV_SIMD128
        size_t step = cv::v_uint8x16::nlanes;//4
        cv::v_uint8x16 v_threshold = cv::v_setall(threshold);
        cv::v_uint8x16 v_255 = cv::v_setall(uchar(255));
        cv::v_uint8x16 v_0 = cv::v_setall(uchar(0));
        for (size_t i = 0; i < mat_size; i++)
        {
            uchar* pg = gray_image.ptr<uchar>(i);
            uchar* pb = binary_image.ptr<uchar>(i);
            
            for (size_t j = 0; j < mat_size - step; j += step)
            {
                cv::v_uint8x16 vg = cv::v_load(pg + j);
                auto condition = vg > v_threshold;
                cv::v_uint8x16 vb = cv::v_select(condition,v_255 , v_0);
                cv::v_store(pb + j, vb);
            }
            //处理一行尾部未够一个step的像素
            size_t current_j = mat_size - mat_size % step;
            for (size_t j = current_j; j < mat_size; j ++)
            {
                pb[j] = pg[j] > threshold ? 255 : 0;
            }
        }
#endif
    }
    //自适应长度simd优化
    {
        size_t step = cv::v_uint8::nlanes;
        cv::v_uint8 v_threshold = cv::vx_setall(threshold);
        cv::v_uint8 v_255 = cv::vx_setall(uchar(255));
        cv::v_uint8 v_0 = cv::vx_setall(uchar(0));
        for (size_t i = 0; i < mat_size; i++)
        {
            uchar* pg = gray_image.ptr<uchar>(i);
            uchar* pb = binary_image.ptr<uchar>(i);

            for (size_t j = 0; j < mat_size - step; j += step)
            {
                cv::v_uint8 vg = cv::vx_load(pg + j);
                auto condition = vg > v_threshold;
                cv::v_uint8 vb = cv::v_select(condition, v_255, v_0);
                cv::vx_store(pb + j, vb);
            }
            //处理一行尾部未够一个step的像素
            size_t current_j = mat_size - mat_size % step;
            for (size_t j = current_j; j < mat_size; j++)
            {
                pb[j] = pg[j] > threshold ? 255 : 0;
            }
        }
    }
    //优化结合openmp并行
    {
        size_t step = cv::v_uint8::nlanes;
        cv::v_uint8 v_threshold = cv::vx_setall(threshold);
        cv::v_uint8 v_255 = cv::vx_setall(uchar(255));
        cv::v_uint8 v_0 = cv::vx_setall(uchar(0));
        //一般双循环openmp并行用于优化外循环
#pragma omp parallel for
        for (int i = 0; i < mat_size; i++)
        {
            uchar* pg = gray_image.ptr<uchar>(i);
            uchar* pb = binary_image.ptr<uchar>(i);

            for (size_t j = 0; j < mat_size - step; j += step)
            {
                cv::v_uint8 vg = cv::vx_load(pg + j);
                auto condition = vg > v_threshold;
                cv::v_uint8 vb = cv::v_select(condition, v_255, v_0);
                cv::vx_store(pb + j, vb);
            }
            //处理一行尾部未够一个step的像素
            size_t current_j = mat_size - mat_size % step;
            for (size_t j = current_j; j < mat_size; j++)
            {
                pb[j] = pg[j] > threshold ? 255 : 0;
            }
        }
    }
    //展开为一维数组优化
    {
        size_t step = cv::v_uint8::nlanes;
        cv::v_uint8 v_threshold = cv::vx_setall(threshold);
        cv::v_uint8 v_255 = cv::vx_setall(uchar(255));
        cv::v_uint8 v_0 = cv::vx_setall(uchar(0));

        //cv::Mat需要判断内存是否为连续分配的才可以展开
        if(gray_image.isContinuous() && binary_image.isContinuous())
        {
            uchar* pg = gray_image.ptr<uchar>(0);
            uchar* pb = binary_image.ptr<uchar>(0);
            //展开为单循环
            int len = gray_image.rows * gray_image.cols;
#pragma omp parallel for
            for (int j = 0; j < len - step; j += step)
            {
                cv::v_uint8 vg = cv::vx_load(pg + j);
                auto condition = vg > v_threshold;
                cv::v_uint8 vb = cv::v_select(condition, v_255, v_0);
                cv::vx_store(pb + j, vb);
            }
            //处理一行尾部未够一个step的像素
            size_t current_j = len - len % step;
            for (size_t j = current_j; j < len; j++)
            {
                pb[j] = pg[j] > threshold ? 255 : 0;
            }
        }
    }
}

void PointCloudSimd()
{
    //点结构体
    struct Point
    {
        float x;
        float y;
        float z;
        Point(): x(1.0f),y(2.0f),z(3.f){}
        Point(const float& x_, const float& y_, const float& z_): x(x_),y(y_),z(z_){}
    };
    const size_t count = 400;
    std::vector<Point> cloud(count);

    //源程序
    {
        //计算质心
        Point centroid(0,0,0);
        for (size_t i = 0; i < count; i++)
        {
            centroid.x += cloud[i].x;
            centroid.y += cloud[i].y;
            centroid.z += cloud[i].z;
        }
        centroid.x /= count;
        centroid.y /= count;
        centroid.z /= count;

        std::cout << "unoptimization centroid: (" << centroid.x << "," << centroid.y << "," << centroid.z << ")\n";
    }
    //simd固定向量长优化
    {
#ifdef  CV_SIMD128
        cv::v_float32x4 centroidx(0, 0, 0, 0);
        cv::v_float32x4 centroidy(0, 0, 0, 0);
        cv::v_float32x4 centroidz(0, 0, 0, 0);
        for (size_t i = 0; i < count; i += 4)
        {
            //x
            cv::v_float32x4 vax(cloud[i].x, cloud[i + 1].x, cloud[i + 2].x, cloud[i + 3].x);
            //y
            cv::v_float32x4 vay(cloud[i].y, cloud[i + 1].y, cloud[i + 2].y, cloud[i + 3].y);
            //z
            cv::v_float32x4 vaz(cloud[i].z, cloud[i + 1].z, cloud[i + 2].z, cloud[i + 3].z);
            centroidx += vax;
            centroidy += vay;
            centroidz += vaz;
        }
        Point centroid(0, 0, 0);
        centroid.x = cv::v_reduce_sum(centroidx);
        centroid.y = cv::v_reduce_sum(centroidy);
        centroid.z = cv::v_reduce_sum(centroidz);
        size_t current_i = count - count % 4;
        for (size_t i = current_i; i < count; i++)
        {
            centroid.x += cloud[i].x;
            centroid.y += cloud[i].y;
            centroid.y += cloud[i].y;
        }
        centroid.x /= count;
        centroid.y /= count;
        centroid.z /= count;

        std::cout << "128bit simd centroid: (" << centroid.x << "," << centroid.y << "," << centroid.z << ")\n";

#endif //  CV_SIMD128

    }
    //自适应长度simd优化
    {
        cv::v_float32 centroidx = cv::vx_setzero_f32();
        cv::v_float32 centroidy = cv::vx_setzero_f32();
        cv::v_float32 centroidz = cv::vx_setzero_f32();
        int step = cv::v_float32::nlanes;
        for (int i = 0; i < count; i += step)
        {
            cv::v_float32 vax = cv::vx_setzero_f32();
            cv::v_float32 vay = cv::vx_setzero_f32();
            cv::v_float32 vaz = cv::vx_setzero_f32();
            //Point内存布局为{x, y, z}
            //Cloud内存布局则为{x1,y1,z1,x2,y2,z2,...,xn,yn,zn)
            //{x1,y1,z1,x2,y2,z2,...,xn,yn,zn) => v1{x1,x2,...,xn}, v2{y1,y2...yn}, v3{z1,z2,...,zn}
            cv::v_load_deinterleave(&(cloud.begin()._Ptr + i)->x, vax, vay, vaz);
            centroidx += vax;
            centroidy += vay;
            centroidz += vaz;
        }
        Point centroid(0, 0, 0);
        centroid.x = cv::v_reduce_sum(centroidx);
        centroid.y = cv::v_reduce_sum(centroidy);
        centroid.z = cv::v_reduce_sum(centroidz);
        size_t current_i = count - count % 4;
        for (size_t i = current_i; i < count; i++)
        {
            centroid.x += cloud[i].x;
            centroid.y += cloud[i].y;
            centroid.y += cloud[i].y;
        }
        centroid.x /= count;
        centroid.y /= count;
        centroid.z /= count;

        std::cout << "auto simd width centroid: (" << centroid.x << "," << centroid.y << "," << centroid.z << ")\n";
    }

    //自适应长度simd优化结合OpenMP
    {
        cv::v_float32 centroidx = cv::vx_setzero_f32();
        cv::v_float32 centroidy = cv::vx_setzero_f32();
        cv::v_float32 centroidz = cv::vx_setzero_f32();
        int step = cv::v_float32::nlanes;
#pragma omp parallel for
        for (int i = 0; i < count; i += step)
        {
            cv::v_float32 vax = cv::vx_setzero_f32();
            cv::v_float32 vay = cv::vx_setzero_f32();
            cv::v_float32 vaz = cv::vx_setzero_f32();
            //Point内存布局为{x, y, z}
            //Cloud内存布局则为{x1,y1,z1,x2,y2,z2,...,xn,yn,zn)
            //{x1,y1,z1,x2,y2,z2,...,xn,yn,zn) => v1{x1,x2,...,xn}, v2{y1,y2...yn}, v3{z1,z2,...,zn}
            cv::v_load_deinterleave(&(cloud.begin()._Ptr + i)->x, vax, vay, vaz);
            centroidx += vax;
            centroidy += vay;
            centroidz += vaz;
        }
        Point centroid(0, 0, 0);
        centroid.x = cv::v_reduce_sum(centroidx);
        centroid.y = cv::v_reduce_sum(centroidy);
        centroid.z = cv::v_reduce_sum(centroidz);
        size_t current_i = count - count % 4;
        for (size_t i = current_i; i < count; i++)
        {
            centroid.x += cloud[i].x;
            centroid.y += cloud[i].y;
            centroid.y += cloud[i].y;
        }
        centroid.x /= count;
        centroid.y /= count;
        centroid.z /= count;

        std::cout << "simd&openmp centroid: (" << centroid.x << "," << centroid.y << "," << centroid.z << ")\n";
    }
}

int main(int argc, char** argv)
{
    //一维数组Simd优化
    ArraySimd();
    //二维矩阵Simd优化
    MatSimd();
    //三维点云Simd优化
    PointCloudSimd();
    return 0;
}
```
--------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/opencv/opencv-universal-intrinsics-simd-2.html
