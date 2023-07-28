---
title: "hpc优化for循环分支预测3"
categories: [ "c++","HPC" ]
tags: [ "c++","for","hpc","if","else" ]
draft: false
slug: "hpc-branch-prediction-loop-for03"
date: "2022-05-23 22:58:20"
---

## 分支嵌套

有时候程序涉及到嵌套的分支，比如下面的程序

```cpp
for(int i = 0; i < size; i++)
{
    if(a[i] > 500)
    {
        if(b[i] < 800)
        {
            if(c[i] > 100)
            {
                d[i] = e[i];
            }
        }
    }
}
```

程序包含了三层的分支嵌套判断，以上写法程序既不简洁且效率不是最好的，在嵌套的分支目标仅为一个一个结果分支`d[i] = e[i]`的情况下，可以优化为仅有一个ifelse分支的情况

```cpp
for(int i = 0; i < size; i++)
{
    if(a[i] > 500 && b[i] < 800 && c[i] > 100)
    {
        d[i] = e[i];
    }
}
```

进一步还可以优化为不含ifelse的程序

```cpp
for(int i = 0; i < size; i++)
{
    d[i] = (a[i] > 500 && b[i] < 800 && c[i] > 100) ? e[i] : d[i];
}
```

## 合并条件

还有一种情况是多个条件，可以通过运算将其合并为一个条件再做判断

```cpp
for(int i = 0; i < size; i++)
{
    if(0 == a[i] && b[i] == 0 && c[i])
    {
        d[i] = e[i];
    }else
    {
        d[i] = 0;
    }
}
```
如以上需要满足三个整数都等于0为真，其实等价于三个整数或运算结果等于0

```cpp
for(int i = 0; i < size; i++)
{
    //按位或后再与0比较
    if(a[i] | b[i] | c[i] == 0)
    {
        d[i] = e[i];
    }else
    {
        d[i] = 0;
    }
}
```

## 测试结果
```
---------------- test size: 100-----------------------
dt1 = 1 us
dt2 = 1 us
dt3 = 0 us
dt4 = 1 us
---------------- test size: 1000-----------------------
dt1 = 8 us
dt2 = 9 us
dt3 = 6 us
dt4 = 5 us
---------------- test size: 10000-----------------------
dt1 = 21 us
dt2 = 20 us
dt3 = 11 us
dt4 = 10 us
---------------- test size: 100000-----------------------
dt1 = 201 us
dt2 = 198 us
dt3 = 122 us
dt4 = 113 us
---------------- test size: 1000000-----------------------
dt1 = 2211 us
dt2 = 2159 us
dt3 = 1485 us
dt4 = 1461 us
```