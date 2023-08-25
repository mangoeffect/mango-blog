---
title: "平均绝对误差MAE直线拟合"
categories: [ "opencv","算法","c++" ]
tags: [ "最小二乘","拟合","MAE" ]
draft: false
slug: "opencv/mean-absolute-error-line-fit"
date: "2020-05-09 15:06:00"
---

## 简介

先简单介绍了最小二乘直线拟合算法以及其不足之处，针对这不足之处转而介绍平均绝对误差(MAE)直线拟合算法，介绍和对比MAE与最小二乘法的不同之处，借助GSL科学计算库求解MAE损失函数的极值，最后c++编码实现MAE直线拟合并进行实验对比。

## 最小二乘直线拟合的缺点

芒果在此前[最小二乘直线拟合](https://mangoroom.cn/opencv/least-square-method-line-fit.html)一文中已经介绍了基于标准最小二乘的直线拟合算法。该拟合算法原理比较简单，可以简单地概括为：以误差（残差）的平方和构建误差损失函数，此函数为连续可导的二次函数，根据多元函数极值解法求取最小值，得出损失函数的解析解。此算法优点为简单快速，可直接解方程组得到拟合结果。但此方法有一个缺点，由于误差是以平方和的形式统计的，这就导致距离直线越远的点产生的误差会翻倍地扩大，距离直线越近（残差小于1）反而误差翻倍缩小。这在拟合过程中就相当于距离直线越远的点所占权重越大，越能影响直线的拟合。如此一来，一旦拟合的数据包含较多的离群点干扰，则拟合出来的直线就不太准确。先看看对比：

在没有干扰数据时，最小二乘直线拟合效果如下
![最小二乘直线拟合.png][1]
加入定向偏离的离散点干扰数据后，最小二乘直线拟合效果如下
![含干扰数据最小二乘法拟合.png][2]
可以看到直线受到离群点的影响，与我们所期望的有所偏差。

## 平均绝对误差直线拟合

平均绝对误差就克服了这一缺点，摒弃掉残差平方和的损失函数，采用残差的绝对值之和来构建损失函数。从[最小二乘直线拟合](https://mangoroom.cn/opencv/least-square-method-line-fit.html)可知，最小二乘的损失函数为：

$$S_{\epsilon^2} = \sum_{i = 1}^{n}{(y_i - y)}^2$$

若加以平均就变成了均方误差（MSE,Mean Square Error)，

$$S_{\epsilon^2} = \frac{1}{n}\sum_{i = 1}^{n}{(y_i - y)}^2$$

但与最小二乘没多大区别, 而我们这次讨论的平均绝对误差与均方误差的差别就是将残差平方去掉，改为残差绝对值，就得到平均绝对误差（MAE,Mean Absolute Error)。

$$S_{\epsilon^2} = \frac{1}{n}\sum_{i = 1}^{n}|y_i - y|$$

$$ = \frac{1}{n}\sum_{i = 1}^{n}|y_i - （ax_i + b)|$$

得到损失函数后，与最小二乘发一样，我们需要求解$S_{\epsilon^2}$最小值，令$S_{\epsilon^2}$取得最小值的直线方程参数$a,b$就是解。但MAE不是连续可导的，无法通过求偏导的方式求极值，此时可以采用机器学习中的梯度下降法求解，这里芒果借助GSL库的多元函数最小值api求解，求解方法的原理可以理解为一个不断逼近的过程，得到的极值只是近似的不是精确，详细的过程可直接看代码，理论部分自行查阅梯度下降法求极值。

### 代码实现

构造数据

```c++
enum class PointType
{
	LINE = 0,
	LINE_WITH_OUTLIER,
	CIRCLE,
	CIRCLE_WITH_OUTLIER
};

void CreateData(std::vector<cv::Point2d>& pts, PointType pointType)
{
	cv::Point2d center = { 500, 500 };
	
	int radius = 200;

	cv::Point2d lineStart = { 100, 100 };
	
	 
	pts.clear();

	cv::Point2d pt;
	srand((unsigned)time(nullptr));

	switch (pointType)
	{
	case PointType::LINE:
		for (size_t i = 0; i < 300; i++)
		{
			pt.x = lineStart.x + i;
			pt.y = lineStart.x + i + (rand() % 30) - 15;
			pts.push_back(pt);
		}
		break;
	case PointType::LINE_WITH_OUTLIER:
		for (size_t i = 0; i < 300; i++)
		{
			pt.x = lineStart.x + i;
			pt.y = lineStart.x + i + (rand() % 30) - 15;
			pts.push_back(pt);
		}
		lineStart = { 300, 200 };
		for (size_t i = 0; i < 50; i++)
		{
			pt.x = lineStart.x + i;
			pt.y = lineStart.x + i + (rand() % 30) - 15 - 70;
			pts.push_back(pt);
		}
		break;
	case PointType::CIRCLE:
		for (size_t i = 0; i < 360; i++)
		{
			pt.x = center.x + radius * std::cos(i) + (rand() % 30) - 15;
			pt.y = center.y + radius * std::sin(i) + (rand() % 30) - 15;
			pts.push_back(pt);
		}
		break;
	case PointType::CIRCLE_WITH_OUTLIER:
		for (size_t i = 0; i < 360; i++)
		{
			pt.x = center.x + radius * std::cos(i) + (rand() % 30) - 15;
			pt.y = center.y + radius * std::sin(i) + (rand() % 30) - 15;
			pts.push_back(pt);
		}
		// 干扰数据
		center = { 300, 300 };
		radius = 50;
		for (size_t i = 0; i < 36; i++)
		{
			pt.x = center.x + radius * std::cos(i) + (rand() % 30) - 15;
			pt.y = center.y + radius * std::sin(i) + (rand() % 30) - 15;
			pts.push_back(pt);
		}
		break;
	default:
		break;
	}
}
```

MAE直线拟合类定义

```c++
#include <iostream>
#include <cassert>
#include <vector>
#include <cstdlib>
#include <ctime>

#include<opencv2/opencv.hpp>


#include <gsl/gsl_vector.h>
#include <gsl/gsl_multimin.h>

using namespace std;
using namespace cv;

bool LeastSquareLineFit(const std::vector<cv::Point2d>& points, const std::vector<double>& weights, double&a, double& b);
class LineFit
{
public:
	LineFit();
	~LineFit();

	static cv::Ptr<LineFit> CreateInstance()  { return cv::makePtr<LineFit>(); }
	void SetMaxIter(const int& iter) { _maxIter = iter; }

	bool LineFitSolve(const std::vector<cv::Point2d>& points, const std::vector<double>& weights, double&a, double& b);

private:
	int _maxIter;									//迭代最大次数
	gsl_multimin_function _function;				//迭代函数
	gsl_multimin_fminimizer * _fminimizer;			//迭代优化器
	gsl_vector *_startPoint;						//迭代算法的初始值
	gsl_vector *_stepSize;							//迭代算法的初始步长

	void SetStartPoint(const double& a, const double& b);						//设置迭代起始点
	static double L1Distance(const gsl_vector * v, void * params);				//目标函数
};

```

MAE直线拟合类实现

```c++
bool LineFit::LineFitSolve(const std::vector<cv::Point2d>& points, const std::vector<double>& weights, double&a, double& b)
{
	_function.params = (void *)&points;


	// 用最小二乘拟合的结果作为初始值
	if (!LeastSquareLineFit(points, weights, a, b))
	{
		return false;
	}

	// 设置初始值
	SetStartPoint(a, b);

	//经验值，初始步长十分之一 
	gsl_vector_set(_stepSize, 0, a / 10.0);
	gsl_vector_set(_stepSize, 1, b / 10.0);
	
	gsl_multimin_fminimizer_set(_fminimizer, &_function, _startPoint, _stepSize);

	int iter = 0;
	int status;
	do
	{
		iter++;
		status = gsl_multimin_fminimizer_iterate(_fminimizer);
		if (status == GSL_ENOPROG) // 表示无法找到更好的解了
		{
			break;
		}
		double size = gsl_multimin_fminimizer_size(_fminimizer);

		status = gsl_multimin_test_size(size, 1e-2);
	} while (status == GSL_CONTINUE && iter < _maxIter);

	gsl_vector * out = gsl_multimin_fminimizer_x(_fminimizer);

	a = gsl_vector_get(out, 0);
	b = gsl_vector_get(out, 1);	
}



double LineFit::L1Distance(const gsl_vector * v, void * params)
{
	std::vector<cv::Point2d> *vect = static_cast<std::vector<cv::Point2d>*>(params);
	int N = vect->size();

	double a, b;
	a = gsl_vector_get(v, 0);
	b = gsl_vector_get(v, 1);

	double sum = 0;

	for (int i = 0; i < N; i++)
	{
		double yi = vect->at(i).y;
		double fi = a * vect->at(i).x + b;
		double dist = yi - fi;
		sum += std::fabs(dist);
	}
	return sum / N;
}



void LineFit::SetStartPoint(const double& a, const double& b)
{
	gsl_vector_set(_startPoint, 0, a);
	gsl_vector_set(_startPoint, 1, b);
}

bool LeastSquareCirleFit(const std::vector<cv::Point2d>& points, const std::vector<double>& weights, cv::Point2d& circleCenter, double& radius)
{
	//检查输入参数 | Check input parameters
	if (points.size() < 3 || (points.size() != weights.size()))
	{
		return false;
	}

	//构造矩阵 | Construct mat

	double XiSum = 0;
	double Xi2Sum = 0;
	double Xi3Sum = 0;
	double YiSum = 0;
	double Yi2Sum = 0;
	double Yi3Sum = 0;
	double XiYiSum = 0;
	double Xi2YiSum = 0;
	double XiYi2Sum = 0;
	double WiSum = 0;

	for (size_t i = 0; i < points.size(); i++)
	{
		XiSum += points.at(i).x * weights.at(i);
		Xi2Sum += points.at(i).x * points.at(i).x * weights.at(i);
		Xi3Sum += points.at(i).x * points.at(i).x * points.at(i).x * weights.at(i);
		YiSum += points.at(i).y * weights.at(i);
		Yi2Sum += points.at(i).y * points.at(i).y * weights.at(i);
		Yi3Sum += points.at(i).y * points.at(i).y * points.at(i).y * weights.at(i);
		XiYiSum += points.at(i).x * points.at(i).y * weights.at(i);
		Xi2YiSum += points.at(i).x * points.at(i).x * points.at(i).y * weights.at(i);
		XiYi2Sum += points.at(i).x * points.at(i).y * points.at(i).y * weights.at(i);
		WiSum += weights.at(i);
	}
	const int N = 3;
	cv::Mat A = cv::Mat::zeros(N, N, CV_64FC1);
	cv::Mat B = cv::Mat::zeros(N, 1, CV_64FC1);

	A.at<double>(0, 0) = Xi2Sum;
	A.at<double>(0, 1) = XiYiSum;
	A.at<double>(0, 2) = XiSum;

	A.at<double>(1, 0) = XiYiSum;
	A.at<double>(1, 1) = Yi2Sum;
	A.at<double>(1, 2) = YiSum;

	A.at<double>(2, 0) = XiSum;
	A.at<double>(2, 1) = YiSum;
	A.at<double>(2, 2) = WiSum;

	B.at<double>(0, 0) = -(Xi3Sum + XiYi2Sum);
	B.at<double>(1, 0) = -(Xi2YiSum + Yi3Sum);
	B.at<double>(2, 0) = -(Xi2Sum + Yi2Sum);

	//解矩阵 | Solve
	//求解A*X = B | Solve the A*X = B
	cv::Mat X;
	cv::solve(A, B, X, cv::DECOMP_LU);
	double a = X.at<double>(0, 0);
	double b = X.at<double>(1, 0);
	double c = X.at<double>(2, 0);

	//计算圆心和半径 | Calculate center and radius.
	circleCenter.x = -0.5 * a;
	circleCenter.y = -0.5 * b;
	radius = 0.5 * std::sqrt(a * a + b * b - 4 * c);
	return true;
}





LineFit::LineFit()
{
	_maxIter = 100;									// 默认最大迭代 100 步

	_function.n = 2;								//变量个数
	_function.f = L1Distance;

	_startPoint = gsl_vector_alloc(_function.n);
	_stepSize = gsl_vector_alloc(_function.n);

	_fminimizer = gsl_multimin_fminimizer_alloc(gsl_multimin_fminimizer_nmsimplex, 2);
}

LineFit::~LineFit()
{
	// 释放资源
	gsl_vector_free(_startPoint);
	gsl_vector_free(_stepSize);

	gsl_multimin_fminimizer_free(_fminimizer);
}

bool LeastSquareLineFit(const std::vector<cv::Point2d>& points, const std::vector<double>& weights, double&a, double& b)
{
	 // AX = B
    // 构造A矩阵 | Construct A mat
	const int N = 2;
	cv::Mat A = cv::Mat::zeros(N, N, CV_64FC1);

	for (int row = 0; row < A.rows; row++)
	{
		for (int col = 0; col < A.cols; col++)
		{
			for (int k = 0; k < points.size(); k++)
			{
				A.at<double>(row, col) = A.at<double>(row, col) + pow(points[k].x, row + col) * weights[k];
			}
		}
	}

	//构造B矩阵 | Construct B mat
	cv::Mat B = cv::Mat::zeros(N, 1, CV_64FC1);
	for (int row = 0; row < B.rows; row++)
	{

		for (int k = 0; k < points.size(); k++)
		{
			B.at<double>(row, 0) = B.at<double>(row, 0) + pow(points[k].x, row)*points[k].y * weights[k];
		}
	}

	// 求解A*X = B | Solve the A*X = B
	cv::Mat X;
	cv::solve(A, B, X, cv::DECOMP_LU);

	// y = b + ax
	b = X.at<double>(0, 0);
	a = X.at<double>(1, 0);

	return true;
}
```

使用测试

```c++
int main()
{
	cv::Mat img = cv::Mat(800, 1000, CV_8UC3, cv::Scalar::all(255)) ;

	std::vector<cv::Point2d> pts;
	CreateData(pts, PointType::LINE_WITH_OUTLIER);

	for (size_t i = 0; i < pts.size(); i++)
	{
		cv::circle(img, pts.at(i), 1, cv::Scalar(255, 0, 0));
	}
	
	std::vector<double> w(pts.size(), 1);
	cv::Ptr<LineFit> lineFit = LineFit::CreateInstance();
	double a, b;
	double a2, b2;
	clock_t ts = clock();
	LeastSquareLineFit(pts, w, a2, b2);
	clock_t te = clock();
	std::cout << "a:" << a2 << " b: " << b2 << std::endl;
	std::cout << "LeastSquareLineFit cost time: " << static_cast<double>(te - ts) << "ms" << std::endl;

	ts = clock();
	lineFit->LineFitSolve(pts, w, a, b);
	te = clock();

	std::cout << "a:" << a << " b: " << b << std::endl;
	std::cout << "MAELineFit cost time: " << static_cast<double>(te - ts) << "ms" << std::endl;
	cv::Point2d sp2 = { 100,0 };
	cv::Point2d ep2 = { 400,0 };
	sp2.y = sp2.x * a2 + b2;
	ep2.y = ep2.x * a2 + b2;

	cv::Point2d sp = { 100,0 };
	cv::Point2d ep = { 400,0 };
	sp.y = sp.x * a + b;
	ep.y = ep.x * a + b;
	cv::line(img, sp2, ep2, cv::Scalar(0, 0, 255), 2);
	cv::line(img, sp, ep, cv::Scalar(0, 255, 0), 2);
	cv::imshow("MAE Line Fit", img);

	cv::waitKey(0);

	return 0;
}

```
### 实验对比


![含干扰数据拟合对比.png][3]


## References

[【1】平均绝对误差MAE圆拟合](https://mangoroom.cn/opencv/mean-absolute-error-circle-fit.html)


--------------------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/opencv/mean-absolute-error-line-fit.html


  [1]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//mae-linefitting-1.png
  [2]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//mae-linefitting-2.png
  [3]: https://mango-blog-1255355814.cos.ap-guangzhou.myqcloud.com//mae-linefitting-3.png
