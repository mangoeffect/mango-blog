---
title: "c++实现二维卷积"
categories: [ "算法" ]
tags: [ "图像处理","图像处理算法","c++","算法","卷积" ]
draft: false
slug: "convolution-by-cpp"
date: "2019-05-29 08:58:00"
---

## 序

在图像处理以及其他的图像信号处理中，卷积是一个非常重要的运算操作。无论是传统的图像处理算法还是深度学习，卷积都有着非常广泛的应用。本篇记录了使用c++实现二维的卷积运算过程。

## 原理

对于原理的介绍若是想要仔细理解的话芒果推荐去阅读冈萨雷斯写的数字图像处理，里面写的非常详细。若没有课本，也可以通过下面芒果收集的博文资料先了解

### 资料推荐阅读

[百度百科-卷积（一维卷积的理解）](https://baike.baidu.com/item/%E5%8D%B7%E7%A7%AF)

[知乎-如何通俗易懂地理解卷积](https://www.zhihu.com/question/22298352)

芒果比较推荐知乎上面高赞地答案，知乎上的大佬还是非常多的，不少答案都解释得通俗易懂。所以在原理上芒果也就不过多叙述，总结起来的话，**卷积的数学上理解实际上就是一个加权求取平均数的运算**。但可以提一下卷积的应用场景。

### 卷积应用场景

#### 图像处理

卷积在图像处理中，有以下的一些应用

- **提取特征**， 诸如canny算子等，都是通过构造好一定的卷积模板，对图像进行卷积运算操作从而提取特征。
- **滤波**， 滤波也是卷积在图像处理领域的一个重要的应用，许多基于空间的滤波算法都是基于卷积而实现的，例如高斯滤波、均值滤波等。

#### 深度学习

在深度学习中，卷积也有着重要的应用。

- **卷积层**，神经网络中卷积层是非常重要的组成部分，卷积层负责特征的提取。
- **池化层**， 池化层的实现实际上也是一个卷积的运算操作

#### 数据处理中

**卷积在数据处理中用来平滑，卷积有平滑效应和展宽效应。**

- **电路学**：卷积法的原理是根据线性定常电路的性质(齐次性、叠加性、时不变性、积分性等),借助电路的单位冲激响应h(t),求解系统响应的工具，系统的激励一般都可以表示为冲击函数和激励的函数的卷积，而卷积为高等数学中的积分概念。概念中冲击函数的幅度是由每个矩形微元的面积决定的。


## 代码实现

以下是卷积的c++代码实现。

```c++
//二维卷积的实现
#include<cassert>
#include<vector>


void conv2(int** filter, int **mat, int** res, const int filter_rows, const int filter_cols, const int mat_rows, const int mat_cols);//指针数组版本
std::vector<std::vector<int> > conv2(std::vector<std::vector<int> > filter, std::vector<std::vector<int> > mat);//向量版本


int main(void)
{
	return 0;
}//main

void conv2(int** filter, int **mat, int** res, const int filter_rows, const int filter_cols, const int mat_rows, const int mat_cols)
{
	assert(filter_cols < mat_cols && filter_rows < mat_rows);
	for(int i = 0; i < mat_rows - 1; ++i)
		for (int j = 0; j < mat_cols - 1; ++j)
		{
			int tmp = 0;
			for (int m = 0; m < filter_rows; ++m)
				for (int n = 0; n < filter_cols; ++n)
					if(0 <= i -m  && i - m < mat_rows && 0 <= j - n && j - n < mat_cols)
						tmp += filter[m][n] * mat[i - m][j - n];//卷积公式

			res[i][j] = tmp;
		}
}

std::vector<std::vector<int> > conv2(std::vector<std::vector<int> > filter, std::vector<std::vector<int> > mat )//向量版本
{
	const int filter_rows = filter.size();
	const int filter_cols = filter[0].size();

	const int mat_rows = mat.size();
	const int mat_cols = mat[0].size();

	assert(filter_cols < mat_cols && filter_rows < mat_rows);
	std::vector<std::vector<int> > res(mat_rows, std::vector<int>(mat_cols, 0));

	for (int i = 0; i < mat_rows - 1; ++i)
		for (int j = 0; j < mat_cols - 1; ++j)
		{
			int tmp = 0;
			for (int m = 0; m < filter_rows; ++m)
				for (int n = 0; n < filter_cols; ++n)
					if (0 <= i - m && i - m < mat_rows && 0 <= j - n && j - n < mat_cols)
						tmp += filter[m][n] * mat[i - m][j - n];//卷积公式

			res[i][j] = tmp;
		}
	return res;
}
```

## 尾巴

如果要从事数字信号处理或者图像处理以及深度学习相关领域的工作，对于卷积这个基本的运算还是要理解透彻的。接触得越多就越发现卷积在这些领域上的应用真的非常广泛。

--- 

本文由芒果浩明发布，转载需注明来源。
本文链接：https://mangoroom.cn/algorithm/convolution-by-cpp.html