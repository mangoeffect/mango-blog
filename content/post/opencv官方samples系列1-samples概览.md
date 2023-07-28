---
title: "opencv官方samples系列1-samples概览"
categories: [ "opencv" ]
tags: [ "opencv","图像处理","计算机视觉","opencv-samples" ]
draft: false
slug: "opencv-sample1-overview"
date: "2019-05-18 21:02:00"
---

## 序

在学opencv的时候，一般人想到的方式一般可能会有那么几种，一是找教程的视频，二是可能是寻找一本合适的书籍，三也有人习惯直接看博客教程学习的。芒果认为这可能是大多数人选择的学习opencv的方式了，当然这三种方式学习的资料也比较容易找到，尤其是中文资料大部分都有相对应的。但芒果觉得，很多人也许忽略了一种比较重要的学习opencv的途径。这个途径就是利用好opencv官方提供的资源。官方提供的资源是非常有价值的，毕竟没有人比opencv官方团队更了解opencv了。所以通过官方的资料学习的方式去学习opencv，应该是最权威的一个途径。那官方的学习资料有哪些呢，也许你会想到opencv的官方文档，你可能说你早就知道但由于文档是全英文的没去仔细学习。但芒果想说的除了官方的文档opencv document之外，其实还有一份特别好的学习资料，而且这份资料还是一个个实际应用的例子。这份资料就是官方的**samples**。这其实是一份非常值得学习的opencv资料，里面涵盖了绝大部分的opencv使用方法。那在学习这份资料之前，咱们有必要先了解一番。

![图1：opencv](https://mangoroom.cn/usr/uploads/2019/05/2013593432.png)

## 获取samples

获取opencv官方的samples非常地简单，有两种方式可以获得到。一是直接在github的源码主仓库里面，可以通过下载opencv的源码获取，[【opencv的github地址】](https://github.com/opencv/opencv)。下载完成后，查看仓库的内容，里面的samples文件夹便是，打开powershell查看

- **仓库代码目录内容**

```

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        5/18/2019  12:59 AM                3rdparty
d-----        5/18/2019  12:59 AM                apps
d-----        5/18/2019  12:59 AM                cmake
d-----        5/18/2019  12:59 AM                data
d-----        5/18/2019  12:59 AM                doc
d-----        5/18/2019  12:59 AM                include
d-----        5/18/2019  12:59 AM                modules
d-----        5/18/2019  12:59 AM                platforms
d-----        5/18/2019  12:59 AM                samples
------        5/18/2019  12:59 AM            455 .editorconfig
------        5/18/2019  12:59 AM          67744 CMakeLists.txt
------        5/18/2019  12:59 AM            191 CONTRIBUTING.md
------        5/18/2019  12:59 AM           2234 LICENSE
------        5/18/2019  12:59 AM            666 README.md
```

另外一种方式是，通过opencv官方安装包获取。在windows安装后的source文件夹中也包含有,安装完成后查看安装的目录

- **安装source文件夹内容**

```

    Directory: D:\opencv4\opencv\sources


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        1/28/2019  10:56 AM                3rdparty
d-----        1/28/2019  10:56 AM                apps
d-----        1/28/2019  10:56 AM                cmake
d-----        1/28/2019  10:56 AM                data
d-----        1/28/2019  10:56 AM                doc
d-----        1/28/2019  10:57 AM                include
d-----        1/28/2019  10:57 AM                modules
d-----        1/28/2019  10:57 AM                platforms
d-----        1/28/2019  10:57 AM                samples
-a----       10/27/2018  10:33 AM            486 .editorconfig
-a----       12/19/2018  12:42 PM          70296 CMakeLists.txt
-a----        4/11/2018  10:35 AM            194 CONTRIBUTING.md
-a----        4/11/2018  10:35 AM           2275 LICENSE
-a----         9/1/2018   9:50 AM            686 README.md
```

通过这两种方式都可以获取得到samples的文件,这两种方式本质上是一样的（可以看到里面的文件都是一致的）。都是通过获取opencv的源码来获取。samples实际上就是官方的开发团队所给出来的一些使用的示例，故而放在源代码里的一个文件夹中。

## samples目录

获取完成后进去samples文件夹可以查看里面都含有哪些文件，同样地通过powershell的控制台可以查看到，samples文件夹的目录结构如下。

```
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        5/18/2019  12:59 AM                android
d-----        5/18/2019  12:59 AM                cpp
d-----        5/18/2019  12:59 AM                data
d-----        5/18/2019  12:59 AM                directx
d-----        5/18/2019  12:59 AM                dnn
d-----        5/18/2019  12:59 AM                gpu
d-----        5/18/2019  12:59 AM                hal
d-----        5/18/2019  12:59 AM                java
d-----        5/18/2019  12:59 AM                opencl
d-----        5/18/2019  12:59 AM                opengl
d-----        5/18/2019  12:59 AM                openvx
d-----        5/18/2019  12:59 AM                python
d-----        5/18/2019  12:59 AM                tapi
d-----        5/18/2019  12:59 AM                va_intel
d-----        5/18/2019  12:59 AM                winrt
d-----        5/18/2019  12:59 AM                winrt_universal
d-----        5/18/2019  12:59 AM                wp8
------        5/18/2019  12:59 AM           1380 CMakeLists.example.in
------        5/18/2019  12:59 AM           4113 CMakeLists.txt
------        5/18/2019  12:59 AM           1417 samples_utils.cmake
------        5/18/2019  12:59 AM           5773 _winpack_build_sample.cmd
------        5/18/2019  12:59 AM           3182 _winpack_run_python_sample.cmd
```

从上图的目录结构可以看到，opencv的samples文件夹其实囊括了众多的例子，包括android、cpp、data、dnn

gpu、java等等。所以可以发现，其实学opencv,学完这里就差不多了呀！那接下来再通过文件树命令来一览opencv的官方例子。

```
├─android
│  ├─15-puzzle
│  │  ├─.settings
│  │  ├─gradle
│  │  ├─res
│  │  │  ├─drawable
│  │  │  └─values
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─puzzle15
│  ├─camera-calibration
│  │  ├─.settings
│  │  ├─gradle
│  │  ├─res
│  │  │  ├─drawable
│  │  │  ├─layout
│  │  │  ├─menu
│  │  │  └─values
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─cameracalibration
│  ├─color-blob-detection
│  │  ├─.settings
│  │  ├─gradle
│  │  ├─res
│  │  │  ├─drawable
│  │  │  ├─layout
│  │  │  └─values
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─colorblobdetect
│  ├─face-detection
│  │  ├─.settings
│  │  ├─gradle
│  │  ├─jni
│  │  ├─res
│  │  │  ├─drawable
│  │  │  ├─layout
│  │  │  ├─raw
│  │  │  └─values
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─facedetect
│  ├─image-manipulations
│  │  ├─.settings
│  │  ├─gradle
│  │  ├─res
│  │  │  ├─drawable
│  │  │  ├─layout
│  │  │  └─values
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─imagemanipulations
│  ├─mobilenet-objdetect
│  │  ├─gradle
│  │  ├─res
│  │  │  ├─layout
│  │  │  └─values
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─opencv_mobilenet
│  ├─tutorial-1-camerapreview
│  │  ├─.settings
│  │  ├─gradle
│  │  ├─res
│  │  │  ├─drawable
│  │  │  ├─layout
│  │  │  └─values
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─tutorial1
│  ├─tutorial-2-mixedprocessing
│  │  ├─.settings
│  │  ├─gradle
│  │  ├─jni
│  │  ├─res
│  │  │  ├─drawable
│  │  │  ├─layout
│  │  │  └─values
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─tutorial2
│  ├─tutorial-3-cameracontrol
│  │  ├─.settings
│  │  ├─gradle
│  │  ├─res
│  │  │  ├─drawable
│  │  │  ├─layout
│  │  │  └─values
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─tutorial3
│  └─tutorial-4-opencl
│      ├─.settings
│      ├─gradle
│      ├─jni
│      ├─res
│      │  ├─drawable
│      │  ├─layout
│      │  ├─menu
│      │  └─values
│      └─src
│          └─org
│              └─opencv
│                  └─samples
│                      └─tutorial4
├─cpp
│  ├─example_cmake
│  └─tutorial_code
│      ├─calib3d
│      │  ├─camera_calibration
│      │  └─real_time_pose_estimation
│      │      ├─Data
│      │      └─src
│      ├─compatibility
│      ├─core
│      │  ├─AddingImages
│      │  ├─discrete_fourier_transform
│      │  ├─file_input_output
│      │  ├─how_to_scan_images
│      │  ├─how_to_use_OpenCV_parallel_for_
│      │  ├─mat_mask_operations
│      │  ├─mat_operations
│      │  └─mat_the_basic_image_container
│      ├─features2D
│      │  ├─AKAZE_tracking
│      │  ├─feature_description
│      │  ├─feature_detection
│      │  ├─feature_flann_matcher
│      │  ├─feature_homography
│      │  └─Homography
│      ├─gapi
│      │  └─porting_anisotropic_image_segmentation
│      ├─gpu
│      │  ├─gpu-basics-similarity
│      │  └─gpu-thrust-interop
│      ├─HighGUI
│      ├─Histograms_Matching
│      ├─imgcodecs
│      │  └─GDAL_IO
│      ├─ImgProc
│      │  ├─anisotropic_image_segmentation
│      │  ├─basic_drawing
│      │  ├─changing_contrast_brightness_image
│      │  ├─HitMiss
│      │  ├─morph_lines_detection
│      │  ├─motion_deblur_filter
│      │  ├─out_of_focus_deblur_filter
│      │  ├─periodic_noise_removing_filter
│      │  ├─Pyramids
│      │  └─Smoothing
│      ├─ImgTrans
│      ├─introduction
│      │  ├─display_image
│      │  ├─documentation
│      │  └─windows_visual_studio_opencv
│      ├─ml
│      │  ├─introduction_to_pca
│      │  ├─introduction_to_svm
│      │  └─non_linear_svms
│      ├─objectDetection
│      ├─photo
│      │  ├─decolorization
│      │  ├─hdr_imaging
│      │  ├─non_photorealistic_rendering
│      │  └─seamless_cloning
│      ├─ShapeDescriptors
│      ├─snippets
│      ├─TrackingMotion
│      ├─video
│      ├─videoio
│      │  ├─video-input-psnr-ssim
│      │  └─video-write
│      └─xfeatures2D
├─data
│  └─dnn
├─directx
├─dnn
│  └─face_detector
├─gpu
├─hal
│  ├─c_hal
│  └─slow_hal
├─java
│  ├─ant
│  │  └─src
│  ├─clojure
│  │  └─simple-sample
│  │      ├─resources
│  │      │  └─images
│  │      ├─src
│  │      │  └─simple_sample
│  │      └─test
│  │          └─simple_sample
│  ├─eclipse
│  │  └─HelloCV
│  │      ├─.settings
│  │      └─src
│  ├─sbt
│  │  ├─lib
│  │  ├─project
│  │  ├─sbt
│  │  └─src
│  │      └─main
│  │          ├─java
│  │          ├─resources
│  │          └─scala
│  └─tutorial_code
│      ├─core
│      │  ├─AddingImages
│      │  ├─discrete_fourier_transform
│      │  ├─mat_mask_operations
│      │  └─mat_operations
│      ├─features2D
│      │  ├─akaze_matching
│      │  ├─feature_description
│      │  ├─feature_detection
│      │  ├─feature_flann_matcher
│      │  └─feature_homography
│      ├─highgui
│      │  └─trackbar
│      ├─Histograms_Matching
│      │  ├─back_projection
│      │  ├─histogram_calculation
│      │  ├─histogram_comparison
│      │  └─histogram_equalization
│      ├─ImgProc
│      │  ├─BasicGeometricDrawing
│      │  ├─changing_contrast_brightness_image
│      │  ├─erosion_dilatation
│      │  ├─HitMiss
│      │  ├─morph_lines_detection
│      │  ├─opening_closing_hats
│      │  ├─Pyramids
│      │  ├─Smoothing
│      │  ├─threshold
│      │  ├─threshold_inRange
│      │  └─tutorial_template_matching
│      ├─ImgTrans
│      │  ├─canny_detector
│      │  ├─distance_transformation
│      │  ├─Filter2D
│      │  ├─HoughCircle
│      │  ├─HoughLine
│      │  ├─LaPlace
│      │  ├─MakeBorder
│      │  ├─remap
│      │  ├─SobelDemo
│      │  └─warp_affine
│      ├─introduction
│      │  └─documentation
│      ├─ml
│      │  ├─introduction_to_pca
│      │  ├─introduction_to_svm
│      │  └─non_linear_svms
│      ├─objectDetection
│      │  └─cascade_classifier
│      ├─photo
│      │  └─hdr_imaging
│      ├─ShapeDescriptors
│      │  ├─bounding_rects_circles
│      │  ├─bounding_rotated_ellipses
│      │  ├─find_contours
│      │  ├─hull
│      │  ├─moments
│      │  └─point_polygon_test
│      ├─TrackingMotion
│      │  ├─corner_subpixels
│      │  ├─generic_corner_detector
│      │  ├─good_features_to_track
│      │  └─harris_detector
│      └─video
│          └─background_subtraction
├─opencl
├─opengl
├─openvx
├─python
│  └─tutorial_code
│      ├─core
│      │  ├─AddingImages
│      │  ├─discrete_fourier_transform
│      │  ├─mat_mask_operations
│      │  └─mat_operations
│      ├─features2D
│      │  ├─akaze_matching
│      │  ├─feature_description
│      │  ├─feature_detection
│      │  ├─feature_flann_matcher
│      │  └─feature_homography
│      ├─highgui
│      │  └─trackbar
│      ├─Histograms_Matching
│      │  ├─back_projection
│      │  ├─histogram_calculation
│      │  ├─histogram_comparison
│      │  └─histogram_equalization
│      ├─imgProc
│      │  ├─BasicGeometricDrawing
│      │  ├─changing_contrast_brightness_image
│      │  ├─erosion_dilatation
│      │  ├─HitMiss
│      │  ├─hough_line_transform
│      │  ├─match_template
│      │  ├─morph_lines_detection
│      │  ├─opening_closing_hats
│      │  ├─Pyramids
│      │  ├─Smoothing
│      │  ├─threshold
│      │  └─threshold_inRange
│      ├─ImgTrans
│      │  ├─canny_detector
│      │  ├─distance_transformation
│      │  ├─Filter2D
│      │  ├─HoughCircle
│      │  ├─HoughLine
│      │  ├─LaPlace
│      │  ├─MakeBorder
│      │  ├─remap
│      │  ├─SobelDemo
│      │  └─warp_affine
│      ├─introduction
│      │  └─documentation
│      ├─ml
│      │  ├─introduction_to_pca
│      │  ├─introduction_to_svm
│      │  ├─non_linear_svms
│      │  └─py_svm_opencv
│      ├─objectDetection
│      │  └─cascade_classifier
│      ├─photo
│      │  └─hdr_imaging
│      ├─ShapeDescriptors
│      │  ├─bounding_rects_circles
│      │  ├─bounding_rotated_ellipses
│      │  ├─find_contours
│      │  ├─hull
│      │  ├─moments
│      │  └─point_polygon_test
│      ├─TrackingMotion
│      │  ├─corner_subpixels
│      │  ├─generic_corner_detector
│      │  ├─good_features_to_track
│      │  └─harris_detector
│      └─video
│          └─background_subtraction
├─tapi
├─va_intel
├─winrt
│  ├─FaceDetection
│  │  └─FaceDetection
│  │      └─Assets
│  ├─ImageManipulations
│  │  ├─assets
│  │  ├─common
│  │  ├─MediaExtensions
│  │  │  ├─Common
│  │  │  └─OcvTransform
│  │  └─sample-utils
│  ├─JavaScript
│  │  ├─css
│  │  ├─html
│  │  ├─images
│  │  ├─js
│  │  └─sample-utils
│  └─OcvImageProcessing
│      └─OcvImageProcessing
│          ├─Assets
│          └─Common
├─winrt_universal
│  ├─PhoneTutorial
│  │  └─Assets
│  └─VideoCaptureXAML
│      └─video_capture_xaml
│          ├─video_capture_xaml.Shared
│          ├─video_capture_xaml.Windows
│          │  └─Assets
│          └─video_capture_xaml.WindowsPhone
│              └─Assets
└─wp8
    ├─OcvImageManipulation
    │  └─PhoneXamlDirect3DApp1
    │      ├─PhoneXamlDirect3DApp1
    │      │  ├─Assets
    │      │  │  └─Tiles
    │      │  ├─Properties
    │      │  └─Resources
    │      └─PhoneXamlDirect3DApp1Comp
    ├─OcvRotatingCube
    │  └─PhoneXamlDirect3DApp1
    │      ├─PhoneXamlDirect3DApp1
    │      │  ├─Assets
    │      │  │  └─Tiles
    │      │  ├─Properties
    │      │  └─Resources
    │      └─PhoneXamlDirect3DApp1Comp
    └─OpenCVXaml
        ├─OpenCVComponent
        └─OpenCVXaml
            ├─Assets
            │  └─Tiles
            ├─Properties
            └─Resources
```

通过上面的文件树，可以查阅opencv的例子都有哪些。例子都是可以直接对应的英文直译，直接可以从文件名了解到例子的应用场景。

## cpp例子概览

作为最重要的c++例子，再来看一下cpp的例子有哪些。

```
    Directory: D:\opencv4\opencv\sources\samples\cpp


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        1/28/2019  10:57 AM                example_cmake
d-----        1/28/2019  10:56 AM                tutorial_code
-a----        4/11/2018  10:35 AM          12705 3calibration.cpp
-a----       11/18/2018   2:59 AM           2689 application_trace.cpp
-a----       11/18/2018   2:59 AM           4224 bgfg_segm.cpp
-a----       11/18/2018   2:59 AM          23048 calibration.cpp
-a----        4/11/2018  10:35 AM           6957 camshiftdemo.cpp
-a----       11/18/2018   2:59 AM           7667 cloning_demo.cpp
-a----       11/18/2018   2:59 AM          15258 cloning_gui.cpp
-a----        11/9/2018  12:08 PM           1742 CMakeLists.txt
-a----       11/18/2018   2:59 AM           1994 connected_components.cpp
-a----        4/11/2018  10:35 AM           3283 contours2.cpp
-a----        4/11/2018  10:35 AM           1574 convexhull.cpp
-a----        4/11/2018  10:35 AM           2095 cout_mat.cpp
-a----       11/18/2018   2:59 AM           2903 create_mask.cpp
-a----       11/18/2018   2:59 AM           3142 dbt_face_detection.cpp
-a----        4/11/2018  10:35 AM           4190 delaunay2.cpp
-a----       11/18/2018   2:59 AM           2647 demhist.cpp
-a----       11/18/2018   2:59 AM           7471 detect_blob.cpp
-a----       11/18/2018   2:59 AM          14931 detect_mser.cpp
-a----       11/18/2018   2:59 AM           2345 dft.cpp
-a----       11/18/2018   2:59 AM           5412 distrans.cpp
-a----       11/10/2018   1:16 PM           1689 dis_opticalflow.cpp
-a----        4/11/2018  10:35 AM           5645 drawing.cpp
-a----       11/18/2018   2:59 AM           2320 edge.cpp
-a----        6/27/2018  10:55 AM           2457 ela.cpp
-a----        4/11/2018  10:35 AM           2065 em.cpp
-a----       11/18/2018   2:59 AM           8950 facedetect.cpp
-a----       11/18/2018   2:59 AM           8481 facial_features.cpp
-a----       11/18/2018   2:59 AM           3536 falsecolor.cpp
-a----        4/11/2018  10:35 AM           1883 fback.cpp
-a----       11/18/2018   2:59 AM           5023 ffilldemo.cpp
-a----       11/18/2018   2:59 AM           4916 filestorage.cpp
-a----       11/18/2018   2:59 AM          10800 fitellipse.cpp
-a----       11/18/2018   2:59 AM           9585 grabcut.cpp
-a----        4/11/2018  10:35 AM           1369 imagelist_creator.cpp
-a----       11/18/2018   2:59 AM           2258 imagelist_reader.cpp
-a----       11/18/2018   2:59 AM          13839 image_alignment.cpp
-a----       11/18/2018   2:59 AM           2510 inpaint.cpp
-a----        4/11/2018  10:35 AM           3796 kalman.cpp
-a----        4/11/2018  10:35 AM           2675 kmeans.cpp
-a----       11/18/2018   2:59 AM           2740 laplace.cpp
-a----       11/18/2018   2:59 AM          18408 letter_recog.cpp
-a----        4/11/2018  10:35 AM           4233 lkdemo.cpp
-a----       11/18/2018   2:59 AM           6903 logistic_regression.cpp
-a----       11/18/2018   2:59 AM           2221 lsd_lines.cpp
-a----       11/18/2018   2:59 AM           2269 mask_tmpl.cpp
-a----       11/18/2018   2:59 AM           8407 matchmethod_orb_akaze_brisk.cpp
-a----       11/18/2018   2:59 AM           2308 minarea.cpp
-a----       11/18/2018   2:59 AM           3233 morphology2.cpp
-a----        4/11/2018  10:35 AM           1784 neural_network.cpp
-a----       11/18/2018   2:59 AM           2412 npr_demo.cpp
-a----        4/11/2018  10:35 AM            968 opencv_version.cpp
-a----       11/11/2018   1:46 PM           6034 pca.cpp
-a----       11/18/2018   2:59 AM           4347 peopledetect.cpp
-a----        4/11/2018  10:35 AM           1283 phase_corr.cpp
-a----       11/18/2018   2:59 AM          11108 points_classifier.cpp
-a----       11/18/2018   2:59 AM           4431 polar_transforms.cpp
-a----       11/18/2018   2:59 AM           5744 qrcode.cpp
-a----       11/18/2018   2:59 AM           3261 segment_objects.cpp
-a----       11/18/2018   2:59 AM          21361 select3dobj.cpp
-a----       11/18/2018   2:59 AM           7475 smiledetect.cpp
-a----       11/18/2018   2:59 AM           5571 squares.cpp
-a----       11/18/2018   2:59 AM          13440 stereo_calib.cpp
-a----       11/18/2018   2:59 AM           9626 stereo_match.cpp
-a----       11/18/2018   2:59 AM           3448 stitching.cpp
-a----       11/18/2018   2:59 AM          30955 stitching_detailed.cpp
-a----         8/8/2018   9:38 AM          13818 train_HOG.cpp
-a----        4/11/2018  10:35 AM           6255 train_svmsgd.cpp
-a----        4/11/2018  10:35 AM           2938 travelsalesman.cpp
-a----        4/11/2018  10:35 AM           4061 tree_engine.cpp
-a----        6/26/2018  11:17 AM           1557 videocapture_basic.cpp
-a----        7/10/2018  10:45 AM           2670 videocapture_camera.cpp
-a----       11/18/2018   2:59 AM          14446 videocapture_gphoto2_autofocus.cpp
-a----       11/18/2018   2:59 AM          13156 videocapture_gstreamer_pipeline.cpp
-a----       11/18/2018   2:59 AM           1566 videocapture_image_sequence.cpp
-a----       11/18/2018   2:59 AM          13707 videocapture_intelperc.cpp
-a----       11/18/2018   2:59 AM          11099 videocapture_openni.cpp
-a----        4/11/2018  10:35 AM           3297 videocapture_starter.cpp
-a----        4/11/2018  10:35 AM           2067 videowriter_basic.cpp
-a----       11/18/2018   2:59 AM           6623 warpPerspective_demo.cpp
-a----       11/18/2018   2:59 AM           4547 watershed.cpp
```

再看看tutorial_code文件夹

```
├─example_cmake
└─tutorial_code
    ├─calib3d
    │  ├─camera_calibration
    │  └─real_time_pose_estimation
    │      ├─Data
    │      └─src
    ├─compatibility
    ├─core
    │  ├─AddingImages
    │  ├─discrete_fourier_transform
    │  ├─file_input_output
    │  ├─how_to_scan_images
    │  ├─how_to_use_OpenCV_parallel_for_
    │  ├─mat_mask_operations
    │  ├─mat_operations
    │  └─mat_the_basic_image_container
    ├─features2D
    │  ├─AKAZE_tracking
    │  ├─feature_description
    │  ├─feature_detection
    │  ├─feature_flann_matcher
    │  ├─feature_homography
    │  └─Homography
    ├─gapi
    │  └─porting_anisotropic_image_segmentation
    ├─gpu
    │  ├─gpu-basics-similarity
    │  └─gpu-thrust-interop
    ├─HighGUI
    ├─Histograms_Matching
    ├─imgcodecs
    │  └─GDAL_IO
    ├─ImgProc
    │  ├─anisotropic_image_segmentation
    │  ├─basic_drawing
    │  ├─changing_contrast_brightness_image
    │  ├─HitMiss
    │  ├─morph_lines_detection
    │  ├─motion_deblur_filter
    │  ├─out_of_focus_deblur_filter
    │  ├─periodic_noise_removing_filter
    │  ├─Pyramids
    │  └─Smoothing
    ├─ImgTrans
    ├─introduction
    │  ├─display_image
    │  ├─documentation
    │  └─windows_visual_studio_opencv
    ├─ml
    │  ├─introduction_to_pca
    │  ├─introduction_to_svm
    │  └─non_linear_svms
    ├─objectDetection
    ├─photo
    │  ├─decolorization
    │  ├─hdr_imaging
    │  ├─non_photorealistic_rendering
    │  └─seamless_cloning
    ├─ShapeDescriptors
    ├─snippets
    ├─TrackingMotion
    ├─video
    ├─videoio
    │  ├─video-input-psnr-ssim
    │  └─video-write
    └─xfeatures2D
```

浏览一遍后你会发现，这几乎囊括了opencv所有模块的使用教程例子，这其实已经比市面上绝大多数的教程要多了。

## 尾巴

通过以上的samples的一个大概的浏览，可以知道opencv官方的samples其实是一个非常好的学习资料的。芒果计划着将其通读一遍，以更好地学习和熟悉opencv。然而以上的例子均为英文注释的，所以我也打算一遍学习将其一遍翻译了，后续将一步步在芒果的个人博客更新。

---

本文由芒果浩明发布，转载需注明来源。
本文链接：https://mangoroom.cn/opencv/opencv-sample1-overview.html