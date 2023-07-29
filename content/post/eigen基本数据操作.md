---
title: "eigen基本数据操作"
categories: [ "Eigen" ]
tags: [ "eigen" ]
draft: false
slug: "Eigen/data-type-manipulation-in-eigen"
date: "2021-10-12 18:31:00"
---

![eigen](https://mangoroom.cn/usr/uploads/2021/09/1136542323.jpg)
练习代码：

```cpp
/**
 * @file main.cpp
 * @author your name (you@domain.com)
 * @brief 学习eigen: 数据类型的基本操作
 * @version 0.1
 * @date 2021-10-12
 * 
 * @copyright Copyright (c) 2021
 * 
 */

#include <iostream>

#include "Eigen/Dense" //稠密矩阵相关定义

void PrintTestPlatform()
{
    std::cout << "------------------------test platform info----------------------------"<<std::endl;
    std::cout << "Eigen version : "<< EIGEN_WORLD_VERSION <<"."<< EIGEN_MAJOR_VERSION << "."<< EIGEN_MINOR_VERSION   << std::endl;

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
    std::cout << "------------------------test platform info----------------------------"<<std::endl;
}

int main(int argc, char** argv)
{
    PrintTestPlatform();
    //-----------------------构造函数、初始化--------------------------
    {
        //数组列向量形式vector
        /*
        [1]
        [2]
        ...
        */
        //列向量由Matrix固定列为1，定义行数所得，如
        //typedef Matrix<float, 2, 1> Vector2f;
        Eigen::Vector2f v2f(1.1f, 2.2f);  //构造函数小括号初始化
        Eigen::Vector2d v2d{3.0, 5.0};    //c++11统一初始化格式大括号初始化
        Eigen::Vector2i v2i = {1, 3};     //大括号赋值初始化
        std::cout<< "Vector2f: \n"<<v2f << "\n"
                 << "Vector2d: \n"<<v2d << "\n"
                 << "Vector2i: \n"<<v2i << "\n";
        Eigen::Vector3i v3i;              //未初始化则为随机数值，Eigen不会默认初始化
        std::cout<<" Vector3i before initialize: \n" << v3i << "\n";
        v3i << 1, 2, 3;                   //<<运算符初始化
        Eigen::VectorXi vxi(5);           //未初始化则为随机数值，Eigen不会默认初始化
        std::cout<<" VectorXi before initialize: \n" << vxi << "\n";
        vxi <<1, 2, 3, 4, 5;

        std::cout<< "Vector3i: \n"<<v3i <<"\n"
                 << "VectorXi: \n"<<vxi << "\n";

        Eigen::Vector3f v3fz = Eigen::Vector3f::Zero(); //初始化为0

        std::cout<< "Eigen::Vector3f::Zero(): "<< v3fz << "\n";

        //数组行向量形式
        // [1] [2]
        Eigen::RowVector2f rv2f(2.0f, 4.0f);//typedef Matrix<float, 2, 1> Vector2f;
        Eigen::RowVector2d rv2d{1.0, 3.0};
        Eigen::RowVector2i rv2i = {1, 2};
        Eigen::RowVectorXi rvxi(5);
        rvxi << 1, 2, 3, 4, 5;
        std::cout<< "RowVector2f: "<<rv2f << "\n"
                 << "RowVector2d: "<<rv2d << "\n"
                 << "RowVector2i: "<<rv2i << "\n"
                 << "RowVectorXi: "<<rvxi << "\n";

        //稠密矩阵
        //Eigen::Matrix2i m2i(1, 2, 3, 4);       错误，无此构造函数
        //Eigen::Matrix2i m2i = {1, 2, 3, 4};    错误，无此初始化方法
        //Eigen::Matrix2i m2i{1, 2, 3, 4};       错误，无此初始化方法
        Eigen::Matrix2i m2i;                     //<< 运算符初始化
        std::cout<< "Matrix2i before initialize: \n" <<m2i <<"\n";
        m2i << 1, 2, 3, 4;
        //1 2
        //3 4
        std::cout<< "Matrix2i: \n" <<m2i <<"\n";

        Eigen::Matrix3i m3i;
        std::cout<< "Matrix3i before initialize: \n" <<m3i <<"\n";
        m3i << 1, 2, 3,
               4, 5, 6,
               7, 8, 9;
        std::cout<< "Matrix3i: \n" <<m3i <<"\n";

        Eigen::MatrixXi mat_xi(2, 4);           //构造2x4静态矩阵 
        std::cout<< "MatrixXi before initialize: \n" <<mat_xi <<"\n";
        mat_xi << 1, 2, 3, 4,
                  5, 6, 7, 8;
        std::cout<< "MatrixXi: \n" <<mat_xi <<"\n";

        //初始化为0
        Eigen::Matrix3i m3iz = Eigen::Matrix3i::Zero();
        std::cout<<"Eigen::Matrix3i::Zero():\n"<<m3iz << "\n"; 

        //获取最大静态矩阵尺寸
        //int max_index = (std::size_t(1) << (8 * sizeof(int) - 1)) - 1;
        //size_t max_size =  std::sqrt(max_index) - 1;
        //std::cout<<"max size: "<< max_size << std::endl;
        //Eigen::MatrixXi max_mat(max_size, max_size);

        //单位矩阵
        Eigen::Matrix4f mat_identity = Eigen::Matrix4f::Identity();
        std::cout<<"Eigen::Matrix4f::Identity(): \n" << mat_identity << std::endl;
    }

    //------------------------赋值、取值---------------------------------
    {
        const size_t size = 100;
        Eigen::MatrixXi mat(size, size);

        //单个元素取值、赋值，矩阵索引从左上角(0, 0)开始
        mat(0, 0) = 1;
        std::cout << "mat(0,0): "<< mat(0, 0) << std::endl;

        //set方法赋值
        Eigen::Matrix4i mat4i = Eigen::Matrix4i::Identity();
        std::cout<< "mat4i before set ones:\n" << mat4i << std::endl;
        //全部元素置为1
        mat4i.setOnes();
        std::cout<< "mat4i after set ones:\n" << mat4i << std::endl;
        //全部元素置为0
        mat4i.setZero();
        std::cout<< "mat4i after set zeros:\n" << mat4i << std::endl;
        //随机数
        mat4i.setRandom();
        std::cout<< "mat4i after set random:\n" << mat4i << std::endl;

        //区域块取值、赋值
        //从位置（10,10)起，往右下角方向取6x8区域块元素
        std::cout << mat.block(10, 10, 6, 8) << std::endl;
        Eigen::MatrixXi tmp_mat(6, 8);
        tmp_mat.setOnes();
        //现有matrix赋值给区块
        mat.block(10, 10, 6, 8) = tmp_mat;
        std::cout << mat.block(10, 10, 6, 8) << std::endl;
        //直接操作设置区块
        mat.block(10, 10, 6, 8).setZero();
        std::cout << mat.block(10, 10, 6, 8) << std::endl;

        //左上角为起点
        mat.topLeftCorner(4, 4) = Eigen::Matrix4i::Zero();
        std::cout<< "mat.topLeftCorner(4, 4):\n"<< mat.topLeftCorner(4, 4) << std::endl;

        //右上角为起点
        mat.topRightCorner(4, 4) = Eigen::Matrix4i::Zero();
        std::cout<< " mat.topRightCorner(4, 4):\n" <<  mat.topRightCorner(4, 4) << std::endl;

        //左下角为起点
        mat.bottomLeftCorner(4, 4) = Eigen::Matrix4i::Ones();
        std::cout<<" mat.bottomLeftCorner(4, 4):\n"<<  mat.bottomLeftCorner(4, 4) << std::endl;

        //右下角为起点
        mat.bottomRightCorner(4, 4) = Eigen::Matrix4i::Ones();
        //右下角为起点
        std::cout<<" mat.bottomRightCorner(4, 4)\n"<< mat.bottomRightCorner(4, 4)<< std::endl;
    }

    //-----------------------基本运算-----------------------------------
    {
        //数乘
        Eigen::Vector3f v3f = Eigen::Vector3f::Ones();
        auto v2 = 3.0f * v3f;
        std::cout<<v2 << std::endl;
        Eigen::Matrix4i mat4i = Eigen::Matrix4i::Ones();
        auto m1 = 5 * mat4i; //或者 auto r1 = mat4i * 5;
        std::cout << m1 << std::endl;
        
        //加减乘
        Eigen::Matrix3i m3i_1 = Eigen::Matrix3i::Ones();
        Eigen::Matrix3i m3i_2 = Eigen::Matrix3i::Identity();
        
        auto add = m3i_1 + m3i_2;
        std::cout<< "add:\n" << add << std::endl;
        auto sub = m3i_1 - m3i_2;
        std::cout<< "sub:\n" << sub << std::endl;
        auto mul = m3i_1 * m3i_2;
        std::cout<< "mul:\n" << mul << std::endl;

        //求逆
        //Eigen::Matrix3i m3i; 非浮点类型数据不可逆，编译不通过
        Eigen::Matrix3f m3f;
        m3f << 1, 0, 2,
               2, 3, 0,
               0, 2, 4;
        std::cout << "inv:\n"<<m3f.inverse() << std::endl;

        //转置
        std::cout<< m3f.transpose() << std::endl;

        //元素和
        std::cout<< m3i_2.sum() << std::endl;

        //元素均值
        std::cout<< m3f.mean() << std::endl;

        //行列式
        std::cout<< m3i_1.determinant() << std::endl;
    }

    return 0;
}
```
运行结果

```
------------------------test platform info----------------------------
Eigen version : 3.3.9
System : Windows
Compiler : msvc 1929
------------------------test platform info----------------------------
Vector2f:
1.1
2.2
Vector2d:
3
5
Vector2i:
1
3
 Vector3i before initialize:
-858993460
-858993460
-858993460
 VectorXi before initialize:
-842150451
-842150451
-842150451
-842150451
-842150451
Vector3i:
1
2
3
VectorXi:
1
2
3
4
5
Eigen::Vector3f::Zero(): 0
0
0
RowVector2f: 2 4
RowVector2d: 1 3
RowVector2i: 1 2
RowVectorXi: 1 2 3 4 5
Matrix2i before initialize:
-858993460 -858993460
-858993460 -858993460
Matrix2i:
1 2
3 4
Matrix3i before initialize:
-858993460 -858993460 -858993460
-858993460 -858993460 -858993460
-858993460 -858993460 -858993460
Matrix3i:
1 2 3
4 5 6
7 8 9
MatrixXi before initialize:
-842150451 -842150451 -842150451 -842150451
-842150451 -842150451 -842150451 -842150451
MatrixXi:
1 2 3 4
5 6 7 8
Eigen::Matrix3i::Zero():
0 0 0
0 0 0
0 0 0
Eigen::Matrix4f::Identity():
1 0 0 0
0 1 0 0
0 0 1 0
0 0 0 1
mat(0,0): 1
mat4i before set ones:
1 0 0 0
0 1 0 0
0 0 1 0
0 0 0 1
mat4i after set ones:
1 1 1 1
1 1 1 1
1 1 1 1
1 1 1 1
mat4i after set zeros:
0 0 0 0
0 0 0 0
0 0 0 0
0 0 0 0
mat4i after set random:
-16343   2785  10578   6897
  2083   -660   8080    443
-10050  -4906 -10679  -6423
 10116  12974  11761 -15893
-842150451 -842150451 -842150451 -842150451 -842150451 -842150451 -842150451 -842150451
-842150451 -842150451 -842150451 -842150451 -842150451 -842150451 -842150451 -842150451
-842150451 -842150451 -842150451 -842150451 -842150451 -842150451 -842150451 -842150451
-842150451 -842150451 -842150451 -842150451 -842150451 -842150451 -842150451 -842150451
-842150451 -842150451 -842150451 -842150451 -842150451 -842150451 -842150451 -842150451
-842150451 -842150451 -842150451 -842150451 -842150451 -842150451 -842150451 -842150451
1 1 1 1 1 1 1 1
1 1 1 1 1 1 1 1
1 1 1 1 1 1 1 1
1 1 1 1 1 1 1 1
1 1 1 1 1 1 1 1
1 1 1 1 1 1 1 1
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
mat.topLeftCorner(4, 4):
0 0 0 0
0 0 0 0
0 0 0 0
0 0 0 0
 mat.topRightCorner(4, 4):
0 0 0 0
0 0 0 0
0 0 0 0
0 0 0 0
 mat.bottomLeftCorner(4, 4):
1 1 1 1
1 1 1 1
1 1 1 1
1 1 1 1
 mat.bottomRightCorner(4, 4)
1 1 1 1
1 1 1 1
1 1 1 1
1 1 1 1
3
3
3
5 5 5 5
5 5 5 5
5 5 5 5
5 5 5 5
add:
2 1 1
1 2 1
1 1 2
sub:
0 1 1
1 0 1
1 1 0
mul:
1 1 1
1 1 1
1 1 1
inv:
 0.6  0.2 -0.3
-0.4  0.2  0.2
 0.2 -0.1 0.15
1 2 0
0 3 2
2 0 4
3
1.55556
0
```

-------------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/eigen/data-type-manipulation-in-eigen.html