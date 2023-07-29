---
title: "平均绝对误差MAE圆拟合"
categories: [ "opencv","算法","计算机视觉" ]
tags: [ "图像处理","图像处理算法" ]
draft: false
slug: "opencv/mean-absolute-error-circle-fit"
date: "2020-05-08 14:44:00"
---

## 简介

介绍了平均绝对误差MAE（Mean Absolute Error）圆拟合方法，分析和对比了标准最小二乘线性回归圆拟合方法的特点，对比两者在圆拟合时的优缺点。最后编码实现进行实验。

## 平均绝对误差MAE圆拟合

### 均方误差（Mean Square Error，MSE）
在之前的文章中已经介绍了标准最小二乘法的[**圆拟合**](https://mangoroom.cn/opencv/least-square-circle-fit.html)以及[**直线拟合**](https://mangoroom.cn/opencv/least-square-method-line-fit.html)，采用的目标损失函数均为误差的平方和（圆拟合用平方差和近似），其目标函数可以简单地描述为：

$$\sum_{i=1}^n{(y_i - f(x_i))}^2$$

将以上误差平均一下，就是回归模型中常用的均方误差（Maen Square Error， MSE)

$$\frac{1}{n}\sum_{i=1}^n{(y_i - f(x_i))}^2$$

此回归模型有一个特点，由于平方的特点，在拟合中离群点越偏离（误差>1)造成的误差就越大，离群点越偏离距离越小（误差<1）造成的误差就越小。忽略下标 i，n = 1，以 y-f(x) 为横坐标，MSE 为纵坐标，绘制其损失函数的图形
![MSE-loss-function.png][1]

绘图代码
```python
import numpy as np
from matplotlib import pyplot as plt

# MSE
x = np.arange(-10, 10, 0.1)
y = x*x;

plt.figure()
plt.title("Mean Square Error")
plt.plot(x, y)
plt.xlabel('y - (x)')
plt.ylabel('MSE')
plt.show()
```


从中学的数学知识就可以知道，这是一个常见的抛物线。曲线光滑可导，可以直接求取极值点，所以不需要回归迭代求取拟合的结果参数（参考标准最小二乘法的[**圆拟合**](https://mangoroom.cn/opencv/least-square-circle-fit.html)以及[**直线拟合**](https://mangoroom.cn/opencv/least-square-method-line-fit.html))，即便采用迭代的数值方法求取，MSE 随着误差的减小，梯度也在减小，这有利于函数的收敛，也可以较快得到极值。

### 平均绝对误差（Mean Absolute Error，MAE）

平均绝对误差指的就是模型预测值 f(x) 与样本真实值 y 之间距离的平均值。其公式如下所示：

$$\frac{1}{n}\sum_{i=1}^n{|y_i - f(x_i)|}$$

与均方误差相比，平均绝对误差特点是没有平方特性影响，不会在离群点距离大时（误差>1）放大误差影响,但缺点是在离群点误差小时（误差<1)也不会减小误差影响。

![MAE-loss-function.png][2]
绘图代码

```python
import numpy as np
from matplotlib import pyplot as plt

# MAE

x = np.arange(-10, 10, 0.1)
y = abs(x);

plt.figure()
plt.title("Mean Absolute Error")
plt.plot(x, y)
plt.xlabel('y - (x)')
plt.ylabel('MAE')
plt.show()

```

### 平均绝对误差圆拟合

目标损失函数为

$$\frac{1}{n}\sum_{i=n}^n{|\sqrt{(x_i - x_c)^2 + (y_i - y_C)^2} - R|}$$

显然此式也是不可导没有解析解的，只能采用数值的方法进行迭代求解，这里采用GSL科学计算库进行求解。关于GSL的编译和安装在[Windows平台CMake编译GSL ](https://mangoroom.cn/tools/use-cmake-build-gsl-on-windows.html)一文有介绍。下面直接放出代码

声明部分

```c++
// 标准最小二乘法拟合
bool LeastSquareCirleFit(const std::vector<cv::Point2d>& points, const std::vector<double>& weights, cv::Point2d& circleCenter, double& radius);


class CircleFit
{
public:
	CircleFit();
	~CircleFit();

	static cv::Ptr<CircleFit> CreateInstance() { return cv::makePtr<CircleFit>(); }

	void SetMaxIter(const int& iter) { _maxIter = iter; }

	bool CircleFitSolve(const std::vector<cv::Point2d>& points, const std::vector<double>& weights, cv::Point2d& circleCenter, double& radius);


private:
	int _maxIter;									//迭代最大次数
	gsl_multimin_function _function;				//迭代函数
	gsl_multimin_fminimizer * _fminimizer;			//迭代优化器
	gsl_vector *_startPoint;						//迭代算法的初始值
	gsl_vector *_stepSize;							//迭代算法的初始步长

	void SetStartPoint(const cv::Point2d& center, const double& radius);		//设置迭代起始点
	static double L1Distance(const gsl_vector * v, void * params);				//（点到圆心距离 - 半径）目标函数
};

```

实现部分

```c++
bool CircleFit::CircleFitSolve(const std::vector<cv::Point2d>& points, const std::vector<double>& weights, cv::Point2d& circleCenter, double& radius)
{
	_function.params = (void *)&points;

	if (radius < 0)
	{
		// 用最小二乘拟合的结果作为初始值
		if (!LeastSquareCirleFit(points, weights, circleCenter, radius))
		{
			return false;
		}
	}
	// 设置初始值
	SetStartPoint(circleCenter, radius);

	//经验值，初始步长设置为半径的十分之一 
	gsl_vector_set(_stepSize, 0, radius / 10.0);
	gsl_vector_set(_stepSize, 1, radius / 10.0);
	gsl_vector_set(_stepSize, 2, radius / 10.0);

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

	circleCenter.x = gsl_vector_get(out, 0);
	circleCenter.y = gsl_vector_get(out, 1);
	radius = gsl_vector_get(out, 2);

	return true;
}

double CircleFit::L1Distance(const gsl_vector * v, void * params)
{
	std::vector<cv::Point2d> *vect = static_cast<std::vector<cv::Point2d>*>(params);
	int N = vect->size();

	double a, b, r;
	a = gsl_vector_get(v, 0);
	b = gsl_vector_get(v, 1);
	r = gsl_vector_get(v, 2);

	double sum = 0;
	for (int i = 0; i < N; i++)
	{
		double xi = vect->at(i).x - a;
		double yi = vect->at(i).y - b;
		double dist = std::sqrt(xi * xi + yi * yi) - r;
		sum += std::fabs(dist);
	}
	return sum / N;
}

void CircleFit::SetStartPoint(const cv::Point2d& center, const double& radius)
{
	gsl_vector_set(_startPoint, 0, center.x);
	gsl_vector_set(_startPoint, 1, center.y);
	gsl_vector_set(_startPoint, 2, radius);
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

CircleFit::CircleFit()
{
	_maxIter = 100;									// 默认最大迭代 100 步

	_function.n = 3;								//变量个数
	_function.f = L1Distance;

	_startPoint = gsl_vector_alloc(_function.n);
	_stepSize = gsl_vector_alloc(_function.n);

	_fminimizer = gsl_multimin_fminimizer_alloc(gsl_multimin_fminimizer_nmsimplex, 3);
}

CircleFit::~CircleFit()
{
	// 释放资源
	gsl_vector_free(_startPoint);
	gsl_vector_free(_stepSize);

	gsl_multimin_fminimizer_free(_fminimizer);
}
```

### 实验对比

首先创建模拟的拟合离散点数据
![离散点.png][3]

标准最小二乘拟合效果
![最小二乘圆拟合.png][4]
平均绝对误差拟合效果
![MAE.png][5]
两者对比
![无干扰数据对比.png][6]
加入干扰数据
![含干扰离散点.png][7]
标准最小二乘拟合效果
![有数据干扰最小二乘.png][8]
平均绝对误差拟合效果
![有数据干扰MAE.png][9]
两者对比
![有干扰数据对比.png][10]
## References

[【1】MSE, MAE, Huber loss详解](https://www.cnblogs.com/hansjorn/p/11458031.html)
[【2】圆拟合算法（距离之和最小）](https://blog.csdn.net/liyuanbhu/article/details/50890587)

--------------------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/opencv/mean-absolute-error-circle-fit.html

  [1]: https://mangoroom.cn/usr/uploads/2020/05/3884456168.png
  [2]: https://mangoroom.cn/usr/uploads/2020/05/1422078333.png
  [3]: https://mangoroom.cn/usr/uploads/2020/05/1396577890.png
  [4]: https://mangoroom.cn/usr/uploads/2020/05/2213239870.png
  [5]: https://mangoroom.cn/usr/uploads/2020/05/3684407773.png
  [6]: https://mangoroom.cn/usr/uploads/2020/05/771059729.png
  [7]: https://mangoroom.cn/usr/uploads/2020/05/1255984281.png
  [8]: https://mangoroom.cn/usr/uploads/2020/05/860262752.png
  [9]: https://mangoroom.cn/usr/uploads/2020/05/2845814442.png
  [10]: https://mangoroom.cn/usr/uploads/2020/05/1083095948.png