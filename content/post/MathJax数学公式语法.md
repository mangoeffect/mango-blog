---
title: "MathJax数学公式语法"
categories: [ "工具" ]
tags: [ "mathjax" ]
draft: false
slug: "tools/mathjax-mathematical-formula-syntax"
date: "2019-12-31 22:22:00"
---

## MathJax简介
![mathjax-logo.png][1]
MathJax是一个跨浏览器的JavaScript库，它使用MathML、LaTeX和ASCIIMathML标记在Web浏览器中显示数学符号。也是现今很多mathdown采用的数学公式语法拓展，其中较为大家数学和常用的便是LateX的公式语法，写论文的同学想必很熟悉了。本文介绍常用的MathJax数学公式语法。

## MathJax语法

### 基本语法

- 在一行内显示公式

  ​				效果$\sum_{i=0}^n i^2 = \frac{(n^2 + n)(2n+1)}{6}$效果

  实现方法

  ~~~php
  $\sum_{i=0}^n i^2=\frac{(n^2+n)(2n+1)}{6}$
  ~~~

- 另起一行显示公式

  ​				 	$\sum_{i=0}^n i^2 =\frac{(n^2+n)(2n+1)}{6}$

  实现方法

  ~~~php
  $$\sum_{i=0}^n i^2=\frac{(n^2+n)(2n+1)}{6}$$
  ~~~

### 希腊字母

| 显示 | 命令 | 显示 | 命令|
| --- | --- | --- | --- |
|α	|\alpha	|β	|\beta
|γ	|\gamma	|δ	|\delta
|ϵ	|\epsilon	|ζ	|\zeta
|η	|\eta	|θ	|\theta
|ι	|\iota	|κ	|\kappa
|λ	|\lambda	|μ	|\mu
|ν	|\nu	|ξ	|\xi
|π	|\pi	|ρ	|\rho
|σ	|\sigma	|τ	|\tau
|υ	|\upsilon	|ϕ	|\phi
|χ	|\chi	|ψ	|\psi
|ω	|\omega


若要大写，将首字母大写即可，如：

  - \alpha: $\alpha$  , 大写 \Alpha: $\Alpha$

  - \beta: $\beta$,   大写\Beta: $Beta$

  - \omega: $\omega$ , 大写\Omega: $\Omega$

  - \gamma: $\gamma$, 大写\Gamma: $\Gamma$

  - \delta: $\delta$, 大写\Delta: $\Delta$

    ...

### 上下角标

  $i^2$ : 

  ~~~php
  $i^2$
  ~~~

  $x_i$

  ~~~php
  $x_i$
  ~~~

  $x_i^2$

  ~~~php
  $x_i^2$
  ~~~

  $cos^2_\theta$

  ~~~php
  $cos^2_\theta$
  ~~~

#### 上下标组合

  $6^{66}$

  ~~~php
  $6^{66}$
  ~~~

  $x^{y^2}$

  ~~~php
  $x^{y^2}$
  ~~~

### 运算符

 方括号、圆括号照常使用，花括号用作分隔符

  $\frac{(x+y)}{[\alpha+\beta]}$ 

  ~~~php
  $\frac{(x+y)}{[\alpha+\beta]}$
  ~~~

- 求和

  $\sum$

  ~~~php
  $\sum$
  ~~~

- 求积分

  $\int$

  ~~~php
  $\int$
  ~~~

  $\iint$

  ~~~php
  $\iint$
  ~~~

- 连乘

  $\prod$

  ~~~php
  $\prod$
  ~~~

- 分数

  $\frac13$

  ~~~php
  $\frac13$
  ~~~

  $\frac{x+y}{a+b}$

  ~~~php
  $\frac{x+y}{a+b}
  ~~~

  $a\over b$

  ~~~php
  $a\over b$
  ~~~

- 开根号

  $\sqrt{x+y}$

  ~~~php
  $\sqrt{x+y}
  
  ~~~

- 极限

  $$\lim_{x\to\infty}$$ 

  ~~~php
  $$\lim_{x\to\infty}$$
  ~~~

- 空格

  $a\ b$ 

  ~~~php
  $a\ b$
  ~~~

### 矩阵

  $$\begin{matrix} 1&x&x^2\\ 1&y&y^2\\ 1&z&z^2 \end{matrix}$$ 

  ~~~php
  $$\begin{matrix} 1&x&x^2\\ 1&y&y^2\\ 1&z&z^2 \end{matrix}$$
  ~~~

  $$\begin{pmatrix} 1&x&x^2\\ 1&y&y^2\\ 1&z&z^2 \end{pmatrix}$$

  ~~~php
  $$\begin{pmatrix} 1&x&x^2\\ 1&y&y^2\\ 1&z&z^2 \end{pmatrix}$$
  ~~~

   $$\begin{bmatrix} 1&x&x^2\\ 1&y&y^2\\ 1&z&z^2 \end{bmatrix}$$ 

  ~~~php
  $$\begin{bmatrix} 1&x&x^2\\ 1&y&y^2\\ 1&z&z^2 \end{bmatrix}$$
  ~~~

  $$\begin{Bmatrix} 1&x&x^2\\ 1&y&y^2\\ 1&z&z^2 \end{Bmatrix}$$ 

  ~~~php
  $$\begin{Bmatrix} 1&x&x^2\\ 1&y&y^2\\ 1&z&z^2 \end{Bmatrix}$$
  ~~~

  $$\begin{vmatrix} 1&x&x^2\\ 1&y&y^2\\ 1&z&z^2 \end{vmatrix}$$ 

  ~~~php
  $$\begin{vmatrix} 1&x&x^2\\ 1&y&y^2\\ 1&z&z^2 \end{vmatrix}$$
  ~~~

  $\left[ \begin{array}{cc|c} 1&2&3\\\ 2&3&4 \end{array} \right]$ 

  ~~~php
  $\left[ \begin{array}{cc|c} 1&2&3\\\ 2&3&4 \end{array} \right]$
  ~~~

### 分段函数

  $$f(n)=\begin{cases}n/2,&\text{if $n$ is even}\\3n+1,&\text{if $n$ is odd}\end{cases}$$ 

  ~~~php
  $$f(n)=\begin{cases}n/2,&\text{if $n$ is even}\\3n+1,&\text{if $n$ is odd}\end{cases}$$
  ~~~

  $$\left. \begin{array}{1} \text{if $n$ is even:}&n/2 \\ \text{if $n$ is odd:}&3n+1 \end{array} \right\}$$ 

  ~~~php
  $$\left. \begin{array}{1} \text{if $n$ is even:}&n/2 \\ \text{if $n$ is odd:}&3n+1 \end{array} \right\}$$
  ~~~

  
## reference
[【1】MathJax语法-csdn](https://blog.csdn.net/u012302219/article/details/51452649)
[【2】MathJax-维基百科](https://zh.wikipedia.org/wiki/MathJax)
[【3】数学公式语法——Mathjax教程](https://oysz2016.github.io/post/8611e6fb.html)

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/tools/mathjax-mathematical-formula-syntax.html

[1]: https://mangoroom.cn/usr/uploads/2019/12/3471172013.png