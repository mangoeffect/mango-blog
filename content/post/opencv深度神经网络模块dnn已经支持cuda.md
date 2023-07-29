---
title: "opencv深度神经网络模块dnn已经支持cuda"
categories: [ "opencv","计算机视觉" ]
tags: [ "opencv","dnn","cuda" ]
draft: false
slug: "opencv/opencv-dnn-module-have-supported-cuda"
date: "2019-11-11 10:52:00"
---

![opencv_cuda_logos.jpg][1]

图片来源：https://www.pyimagesearch.com/2016/07/11/compiling-opencv-with-cuda-support/

目前（2019.11.11）opencv深度神经网络模块（dnn）已经添加了cuda的支持，一看到这个消息的时候芒果马上去opencv官网以及github，翻了一遍blog、release还有issue等，都没发现，最后这一消息在opencv的文档里得到了证实，原来只是添加到了代码仓库里，还未正式发布。已经发布的opencv4.1.2正式版dnn模块的有关推理计算的枚举定义是如下这个样子：

文档链接[https://docs.opencv.org/4.1.2/d6/d0f/group__dnn.html](https://docs.opencv.org/4.1.2/d6/d0f/group__dnn.html)

```c++
enum  	cv::dnn::Backend {
  cv::dnn::DNN_BACKEND_DEFAULT,
  cv::dnn::DNN_BACKEND_HALIDE,
  cv::dnn::DNN_BACKEND_INFERENCE_ENGINE,
  cv::dnn::DNN_BACKEND_OPENCV,
  cv::dnn::DNN_BACKEND_VKCOM
}

enum  	cv::dnn::Target {
  cv::dnn::DNN_TARGET_CPU,
  cv::dnn::DNN_TARGET_OPENCL,
  cv::dnn::DNN_TARGET_OPENCL_FP16,
  cv::dnn::DNN_TARGET_MYRIAD,
  cv::dnn::DNN_TARGET_VULKAN,
 ```

可以看到截止最发布的正式版（4.1.2）是不支持cuda的，但在最新的opencv4.1.2-dev文档里发现新增了对cuda的支持，最新的文档有关定义如下：

文档链接：[https://docs.opencv.org/master/d6/d0f/group__dnn.html](https://docs.opencv.org/master/d6/d0f/group__dnn.html)

```c++
enum  	cv::dnn::Backend {
  cv::dnn::DNN_BACKEND_DEFAULT,
  cv::dnn::DNN_BACKEND_HALIDE,
  cv::dnn::DNN_BACKEND_INFERENCE_ENGINE,
  cv::dnn::DNN_BACKEND_OPENCV,
  cv::dnn::DNN_BACKEND_VKCOM,
  cv::dnn::DNN_BACKEND_CUDA
}

enum  	cv::dnn::Target {
  cv::dnn::DNN_TARGET_CPU,
  cv::dnn::DNN_TARGET_OPENCL,
  cv::dnn::DNN_TARGET_OPENCL_FP16,
  cv::dnn::DNN_TARGET_MYRIAD,
  cv::dnn::DNN_TARGET_VULKAN,
  cv::dnn::DNN_TARGET_FPGA,
  cv::dnn::DNN_TARGET_CUDA,
  cv::dnn::DNN_TARGET_CUDA_FP16
}
```

可以看到opencv仓库代码里已经添加了对cuda的支持，预计在下一个版本会正式发布。之前还一直觉得由于opencv是intel主导的原因不对cuda支持，现在一补上这一块，opencv在深度学习领域应用开发又大有可为了，真是期待呀。

---

本文由芒果浩明发布，转载请注明来源。
本文链接：https://blog.mangoroom.cn/opencv/opencv-dnn-module-have-supported-cuda.html

  [1]: https://mangoroom.cn/usr/uploads/2019/11/2447171765.jpg