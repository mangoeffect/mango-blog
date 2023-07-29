---
title: "OpenCV程序优化技巧(1)"
categories: [ "opencv","c++" ]
tags: [ "opencv","simd","parallel","optimization" ]
draft: false
slug: "opencv/optimization-methods-in-opencv-1"
date: "2022-03-02 10:14:00"
---

## OpenCV程序效率优化方法


### 使用指针方法遍历像素点

OpenCV中图像的存储对象为Mat类，该类提供了多种方式访问像素的的值。一般来说分为以at方法类与ptr指针的方式访问，相较之下使用指针ptr的访问方式效率将会高一些。尤其是在debug模式之下指针的效率提升更加明显，release模式则相差不大。其中原因是at方法访问像素时包含多一次的边界检查：

```cpp
//at方法实现，返回像素值前有5次断言判断
template<typename _Tp> inline
_Tp& Mat::at(int i0, int i1)
{
    CV_DbgAssert(dims <= 2);
    CV_DbgAssert(data);
    CV_DbgAssert((unsigned)i0 < (unsigned)size.p[0]);
    CV_DbgAssert((unsigned)(i1 * DataType<_Tp>::channels) < (unsigned)(size.p[1] * channels()));
    CV_DbgAssert(CV_ELEM_SIZE1(traits::Depth<_Tp>::value) == elemSize1());
    return ((_Tp*)(data + step.p[0] * i0))[i1];
}

//ptr方法实现，返回像素之前有4次判断
template<typename _Tp> inline
_Tp* Mat::ptr(int i0, int i1)
{
    CV_DbgAssert(dims >= 2);
    CV_DbgAssert(data);
    CV_DbgAssert((unsigned)i0 < (unsigned)size.p[0]);
    CV_DbgAssert((unsigned)i1 < (unsigned)size.p[1]);
    return (_Tp*)(data + i0 * step.p[0] + i1 * step.p[1]);
}
```

所以基于ptr指针的方式访问，在debug模式下效率将会有一定的提升

```cpp
uchar GetGammaTrans(const uchar src)
{
    double gamma = 0.7;
    uchar result = src;
    result = std::min(int(result * std::pow(result / 255.0, gamma)), 255);
    return result;
}

//Access pixels by pointer
void AccessPixelsByPointer()
{
    size_t rows = 2000;
    size_t cols = 2000;
    cv::Mat mono = cv::Mat::zeros(rows, cols, CV_8UC1);

    //acess by at()
    auto t0 = std::chrono::system_clock::now();
    for (size_t i = 0; i < rows; i++)
    {
        for (size_t j = 0; j < cols; j++)
        {
            mono.at<uchar>(i, j) = GetGammaTrans(*mono.ptr<uchar>(i, j));
        } 
    }
    auto t1 = std::chrono::system_clock::now();

    //access by pointer
    for (size_t i = 0; i < rows; i++)
    {
        for (size_t j = 0; j < cols; j++)
        {
            *mono.ptr<uchar>(i, j) = GetGammaTrans(*mono.ptr<uchar>(i, j));
        }
    }
    auto t2 = std::chrono::system_clock::now();

    //access by row-pointer
    for (size_t i = 0; i < rows; i++)
    {
        uchar* row_ptr = mono.ptr<uchar>(i, 0);
        for (size_t j = 0; j < cols; j++)
        {
            row_ptr[j] = GetGammaTrans(*mono.ptr<uchar>(i, j));
        }
    }
    auto t3 = std::chrono::system_clock::now();
    
    std::cout << "Mono image test: \n";
    std::cout << "Access by at() cost "<< std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0).count() << " ms.\n";
    std::cout << "Access by ptr() cost "<< std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << " ms.\n";
    std::cout << "Access by row-ptr cost "<< std::chrono::duration_cast<std::chrono::milliseconds>(t3 - t2).count() << " ms.\n";
}
```

测试结果：

```
//debug
Mono image test:
Access by at() cost 346 ms.
Access by ptr() cost 317 ms.
Access by row-ptr cost 205 ms.

//release
Mono image test:
Access by at() cost 44 ms.
Access by ptr() cost 44 ms.
Access by row-ptr cost 55 ms.
```

### 使用LUT查找表函数

一些重复性的像素处理操作可以基于查找表的思路进行效率提升，OpenCV也提供了查找表的工具函数。查找表的提升原理是预先将结果计算好存放至一张表里，后续使用直接从表里查找结果，无需每次都计算一遍。这种方法常适用于伽马变换、灰度拉伸等针对灰度值固定映射的场景。

```cpp
//Use Lut-table
void UseLutTable()
{
    size_t rows = 2000;
    size_t cols = 2000;
    cv::Mat mono = cv::Mat::zeros(rows, cols, CV_8UC1);

    auto t0 = std::chrono::system_clock::now();
    //Compute the value everytime
    for (size_t i = 0; i < rows; i++)
    {
        for (size_t j = 0; j < cols; j++)
        {
            *mono.ptr<uchar>(i, j) = GetGammaTrans(*mono.ptr<uchar>(i, j));
        }      
    }

    auto t1 = std::chrono::system_clock::now();
    //Use Lut-table
    cv::Mat table = cv::Mat(1, 256, CV_8UC1);
    for (size_t i = 0; i < 256; i++)
    {
        *table.ptr<uchar>(0, i) = GetGammaTrans(i);
    }
    cv::Mat mono_src = cv::Mat::zeros(rows, cols, CV_8UC1);
    cv::LUT(mono_src, table, mono);
    auto t2 = std::chrono::system_clock::now();

    std::cout << "Lut-table test: \n";
    std::cout << "Compute the value everytime cost "<< std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0).count() << " ms.\n";
    std::cout << "Use Lut-table cost "<< std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << " ms.\n";
}
```

测试结果：

```
//debug
Lut-table test:
Compute the value everytime cost 279 ms.
Use Lut-table cost 19 ms.

//release
Lut-table test:
Compute the value everytime cost 44 ms.
Use Lut-table cost 15 ms.
```

### 使用Parallel for并行框架

对于图像处理，循环遍历像素点的操作OpenCV也提供了一个并行循环框架，基于该框架可以充分多核心处理器的并行处理能力，大大提升处理效率。但是使用循环并行框架时，需注意将外循环并行而非内循环并行，内循环并行反而可能适得其反。

```cpp
void ParalleyFor()
{
    size_t rows = 2000;
    size_t cols = 2000;
    cv::Mat mono = cv::Mat::zeros(rows, cols, CV_8UC1);

    auto t0 = std::chrono::system_clock::now();
    //Compute the value everytime
    for (size_t i = 0; i < rows; i++)
    {
        for (size_t j = 0; j < cols; j++)
        {
            *mono.ptr<uchar>(i, j) = GetGammaTrans(*mono.ptr<uchar>(i, j));
        }      
    }

    auto t1 = std::chrono::system_clock::now();
    //Use parallel-for framework
    cv::parallel_for_(cv::Range(0, rows), [&](const cv::Range& range)
    {
        for (size_t i = range.start; i < range.end; i++)
        {
            for (size_t j = 0; j < cols; j++)
            {
                *mono.ptr<uchar>(i, j) = GetGammaTrans(*mono.ptr<uchar>(i, j));
            }
        }
    }); 

    auto t2 = std::chrono::system_clock::now();
    for (size_t i = 0; i < rows; i++)
    {
        cv::parallel_for_(cv::Range(0, cols), [&](const cv::Range& range)
        {
            for (size_t j = range.start; j < range.end; j++)
            {
                *mono.ptr<uchar>(i, j) = GetGammaTrans(*mono.ptr<uchar>(i, j));
            }
        });
    }
    auto t3 = std::chrono::system_clock::now();

    std::cout << "Paralley-for test: \n";
    std::cout << "Compute the value everytime cost "<< std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0).count() << " ms.\n";
    std::cout << "Paralley-for outside loop cost "<< std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << " ms.\n";
    std::cout << "Paralley-for inside loop cost "<< std::chrono::duration_cast<std::chrono::milliseconds>(t3 - t2).count() << " ms.\n";
}
```

测试结果：

```
//debug
Paralley-for test:
Compute the value everytime cost 269 ms.
Paralley-for outside loop cost 51 ms.
Paralley-for inside loop cost 861 ms.

//release
Paralley-for test:
Compute the value everytime cost 46 ms.
Paralley-for outside loop cost 11 ms.
Paralley-for inside loop cost 433 ms.
```

### 循环展开

循环展开一种减少循环次数的优化方法，通过增加循环的累加步长减少循环的次数，减少循环次数的好处就是可以减少循环中的分之预测，从而提升效率。但缺点是使得代码膨胀，以及可读性降低。

```cpp
//Loop unrolling
void LoopUnrolling()
{
    size_t rows = 2000;
    size_t cols = 2000;
    cv::Mat mono = cv::Mat::zeros(rows, cols, CV_8UC1);

    auto t0 = std::chrono::system_clock::now();
    //Origin loop
    for (size_t i = 0; i < rows; i++)
    {
        for (size_t j = 0; j < cols; j ++)
        {
            mono.ptr<uchar>(i, j)[0] = GetGammaTrans(*mono.ptr<uchar>(i, j));
        }
    }
    auto t1 = std::chrono::system_clock::now();
    //Loop unrolling
    for (size_t i = 0; i < rows; i++)
    {
        for (size_t j = 0; j < cols; j += 4)
        {
            mono.ptr<uchar>(i, j)[0] = GetGammaTrans(*mono.ptr<uchar>(i, j));
            mono.ptr<uchar>(i, j)[1] = GetGammaTrans(*mono.ptr<uchar>(i, j + 1));
            mono.ptr<uchar>(i, j)[2] = GetGammaTrans(*mono.ptr<uchar>(i, j + 2));
            mono.ptr<uchar>(i, j)[3] = GetGammaTrans(*mono.ptr<uchar>(i, j + 3));
        }
    }
    auto t2 = std::chrono::system_clock::now();
    std::cout << "Loop unrolling test: \n";
    std::cout << "Origin loop cost " << std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0).count() << " ms.\n";
    std::cout << "Unrolling loop cost " << std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << " ms.\n";
}
```

测试结果：

```
//debug
Loop unrolling test:
Origin loop cost 282 ms.
Unrolling loop cost 274 ms.

//release
Loop unrolling test:
Origin loop cost 46 ms.
Unrolling loop cost 46 ms.
```


### 内存块平铺

利用cpu的缓存优化特性，对于涉及大量数据的内存读写操作，将内存分块访问进行读写有利于提高cpu的缓存命中率，从而提高程序运行性能。而平铺(tile)这一优化技巧就是基于这一原理，但是现代编译器非常智能，在绝大多数场景下都无需程序员手动进行平铺优化，编译器在release优化级别下一般会自动平铺处理。所以一般情况下不建议手动平铺，建议在数据处理逻辑非常复杂的情况下可以尝试手动平铺优化。

```cpp
//Block tile
void BlockTile()
{
    size_t rows = 2400;
    size_t cols = 2400;
    cv::Mat mono = cv::Mat::zeros(rows, cols, CV_8UC1);

    auto t0 = std::chrono::system_clock::now();
    //Origin loop
    for (size_t i = 0; i < rows; i++)
    {
        for (size_t j = 0; j < cols; j++)
        {
            *mono.ptr<uchar>(i, j) = 0;
        }
    }
    auto t1 = std::chrono::system_clock::now();
    //Block tile
    size_t tile_row = 32;
    size_t tile_col = 8;
    for (size_t r = 0; r < rows; r += tile_row)
    {
        for (size_t c = 0; c < cols; c += tile_col)
        {
            for (size_t i = r; i < r + tile_row; i++)
            {
                for (size_t j = c; j < c + tile_col; j++)
                {
                    *mono.ptr<uchar>(i, j) = 0;
                }
            }
        }
    }
    auto t2 = std::chrono::system_clock::now();
    std::cout << "Block tile test: \n";
    std::cout << "Origin loop cost " << std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0).count() << " ms.\n";
    std::cout << "Block tile cost " << std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << " ms.\n";
}


```

测试结果：

```
//debug
Block tile test:
Origin loop cost 114 ms.
Block tile cost 115 ms.

//release
Block tile test:
Origin loop cost 4 ms.
Block tile cost 4 ms.
```

### Simd指令集优化

cpu指令集优化也是提升图像处理效率的一个有效手段，SIMD指令集优化可以实现单条指令一次计算多个数据，也是属于硬件级别的并行处理。不同架构的cpu指令集优化接口并不相同，为此OpenCV提供了统一的指令集优化框架，基于该框架编写指令集优化可以实现x86、arm等平台的加速。

```cpp
//Universal Simd
void  UniversalSimd()
{
    size_t rows = 2400;
    size_t cols = 2400;
    cv::Mat mono = cv::Mat::zeros(rows, cols, CV_8UC1);

    auto t0 = std::chrono::system_clock::now();
    //Origin loop
    for (size_t i = 0; i < rows; i++)
    {
        for (size_t j = 0; j < cols; j++)
        {
            *mono.ptr<uchar>(i, j) = 0;
        }
    }
    auto t1 = std::chrono::system_clock::now();
    //simd
    size_t step = cv::v_uint8::nlanes;
    for (size_t i = 0; i < rows; i++)
    {
        for (size_t j = 0; j < cols; j += step)
        {
            cv::v_uint8 v_src = cv::v_load(mono.ptr<uchar>(i, j));
            v_src = cv::v_setzero_u8();
            cv::v_store(mono.ptr<uchar>(i, j), v_src);
        }
    }
    auto t2 = std::chrono::system_clock::now();
    std::cout << "Simd test: \n";
    std::cout << "Origin loop cost " << std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0).count() << " ms.\n";
    std::cout << "Simd cost " << std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << " ms.\n";
}
```

测试结果：

```
//debug
Simd test:
Origin loop cost 114 ms.
Simd cost 21 ms.

//release
Simd test:
Origin loop cost 5 ms.
Simd cost 0 ms.
```

### Gapi优化框架

GApi是OpenCV 4.x推出的一个新模块，该模块基于图的方式实现程序的效率优化，将每一个处理步骤抽象为一个节点，加入处理流程(graph)，把计算的流程构建成一个有向图后，最后一起计算。

```cpp
void GraphApi()
{
    size_t rows = 4000;
    size_t cols = 4000;
    cv::Mat color = cv::Mat(rows, cols, CV_8UC3);
    cv::circle(color, { 1000, 1000 }, 500, { 255, 200,100 }, 3);

    auto t0 = std::chrono::system_clock::now();
    cv::Mat img_resize;
    cv::resize(color, img_resize, cv::Size(), 0.5, 0.5);
    cv::Mat img_gray;
    cv::cvtColor(img_resize, img_gray, cv::COLOR_BGR2GRAY);
    cv::Mat img_blur;
    cv::blur(img_gray, img_blur, cv::Size(3, 3));
    cv::Mat edge1;
    cv::Canny(img_blur, edge1, 32, 128, 3);

    auto t1 = std::chrono::system_clock::now();
    cv::GMat in;
    cv::GMat vga = cv::gapi::resize(in, cv::Size(), 0.5, 0.5);
    cv::GMat gray = cv::gapi::BGR2Gray(vga);
    cv::GMat blurred = cv::gapi::blur(gray, cv::Size(5, 5));
    cv::GMat out = cv::gapi::Canny(blurred, 32, 128, 3);
    cv::GComputation ac(in, out);
    cv::Mat edge2;
    ac.apply(color, edge2);
    auto t2 = std::chrono::system_clock::now();

    std::cout << "Gapi test: \n";
    std::cout << "Origin proccess cost " << std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0).count() << " ms.\n";
    std::cout << "Gapi proccess cost " << std::chrono::duration_cast<std::chrono::milliseconds>(t2 - t1).count() << " ms.\n";
}
```

测试结果：

```
//debug
Gapi test:
Origin proccess cost 180 ms.
Gapi proccess cost 203 ms.

//release
Gapi test:
Origin proccess cost 23 ms.
Gapi proccess cost 27 ms.
```

------------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/opencv/optimization-methods-in-opencv-1.html