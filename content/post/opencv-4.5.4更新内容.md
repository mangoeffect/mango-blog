---
title: "opencv-4.5.4更新内容"
categories: [ "opencv" ]
tags: [ "opencv","tbb","openmp","openvino","avx" ]
draft: false
slug: "opencv-4-5-4-update-change-log"
date: "2021-10-14 20:51:00"
---

## 概要

opencv4.5.4于20211011发布，仓库发布地址

[https://github.com/opencv/opencv/releases/tag/4.5.4](https://github.com/opencv/opencv/releases/tag/4.5.4)

## 主要更新

- DNN模块新增8bit量化,新增onnx模型导入 
- 改善Julia语言接口绑定，此部分仍在opencv_contib
- 新增语音识别示例
- 针对RISC-V指令集处理器优化DNN模块
- 新增统一向量化指令集优化教程，新增统一跨平台并行for循环优化框架 parallel_for_教程

统一向量指令集优化(跨平台的SIMD)是非常强大的优化手段，结合统一跨平台的for循环优化框架parallel_for_可以对OpenCV程序进行非常可观的效率提升。但使用的时候需要注意应该自行编译opencv使用，编译时需要注意以下两点：

- 将opencv编译指令集基准设置为avx2指令集，avx2比默认sse指令集位宽增大一倍，理论加速就比sse提升一倍。
- 将opencv并行框架设置为openmp或tbb, opencv支持多种并行框架优化，但默认并非openmp或tbb，windows默认微软的并行框架，linux则是pthread。选择opemp或者tbb优化提升更大一些。

## DNN模块

- 改进提升了 layers， activations以及更多模型的支持 :

    - GRU, CumSum, Max, Min, ExpandDims
    - 修复了非对称填充的固定卷积的相关问题
    - 修复了Unsqueeze(ONNX opset 13)
    - 修复了OpenCL内核中的几个内存访问问题
- 为TextRecognitionModel实现了CTC前缀波束搜索解码
- 新增SoftNMS极大值抑制方法的实现
- OpenVINO
    - 更新推理后端OpenVINO版本为OpenVINO 2021.4.1 LTS
    - 添加了对具有not-FP32数据输出的模型或具有1D layout的输出的支持

opencv最近dnn更新都是与OpenVINO相关，作为opencv重要的一个推理后端引擎，可见后续基于cpu的模型推理都离不开OpenVINO了，毕竟OpenCV是由Intel发起和主导的，而OpenVINO是Intel自家的。

## 其他

OpenCV4.5.4剩余的更新部分没什么新增的功能，剩余很大的一部分是关于Gapi框架的bug修复，gapi也是opencv较新的一个模块，用于优化多个算子流程的效率优化。详细的更新参阅OpenCV的change log
[https://github.com/opencv/opencv/wiki/ChangeLog#version454](https://github.com/opencv/opencv/wiki/ChangeLog#version454)

----------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/opencv/opencv-4-5-4-update-change-log.html

