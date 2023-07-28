---
title: "典型的python文件结构"
categories: [ "python" ]
tags: [ "python" ]
draft: false
slug: "typical-python-file-structure"
date: "2019-05-20 15:42:00"
---

## 序

对python不太熟悉，虽然python也非常地简单易学，偏向熟悉c++这种编译类型语言的芒果，对python这种解析型的脚本语言了解不多，一直困惑较多的两个点是python的编码风格该是个什么样子的，一旦代码量较大的项目文件该如何组织。今天在了解main函数时无意中找到了答案。

## 典型的python文件结构

从**《python核心编程（第二版）》**中可以找到答案，典型的规范的python文件结构应当是如下的安排

### 1.起始行

```python
#/usr/bin/env python                                （1）起始行
```

### 2. 模块文档（文档字符串）

```python
“this is a  test module”                            （2）模块文档（文档字符串）
```

### 3. 模块导入

```python
import sys                                          （3）模块导入

import os
```

### 4. （全局）变量定义

```python
debug=True                                          （4）（全局）变量定义
```

### 5. 类定义（若有）     

```python
class Foo Class（object）：                          （5）类定义（若有）         

	“Foo class”

	pass


```

### 6. 函数定义（若有）

```python
def test():                                           （6）函数定义（若有）

	"test function"

	foo=FooClass（）

	if debug：

		print 'ran test（）'
```

### 7. 主程序

```python
if __name__=='__main__':            （7）主程序：如果模块是被导入，__name__的值为模块名字；

	test()                           如果模块是被直接执行，__name__的值为__main__
```



## 尾巴

以上便是典型的python文件结构了，编程需要保持良好的编码习惯，据说有追求的程序员写代码像是在写诗。

---

本文由芒果浩明发布，转载需注明来源。
本文链接：https://mangoroom.cn/python/typical-python-file-structure.html