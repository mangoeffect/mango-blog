---
title: "opencv4.5.3编译库分享"
categories: [ "资源分享" ]
tags: [ "opencv","vs2019","tbb","avx2","32bit" ]
draft: false
slug: "资源分享/opencv-4-5-3-x86-vc16-shared-avx2-world-tbb"
date: "2021-09-13 21:45:00"
---

## 编译信息简介：

| 编译选项    | 说明                       |
|---------|--------------------------|
| 版本号     | 4.5.3                    |
| 位数      | 32bit                    |
| 编译器     | vc16(Visual Studio 2019) |
| 链接形式    | 动态库                      |
| 指令集优化级别 | avx2                     |
| 库文件模块   | 合并到world                 |
| 并行框架    | Intel TBB                   |

## 详细编译选项信息：

```

Selecting Windows SDK version 10.0.17763.0 to target Windows 10.0.19042.
Detected processor: AMD64
sizeof(void) = 4 on 64 bit processor. Assume 32-bit compilation mode
libjpeg-turbo: VERSION = 2.1.0, BUILD = opencv-4.5.3-libjpeg-turbo
Could NOT find OpenJPEG (minimal suitable version: 2.0, recommended version >= 2.3.1). OpenJPEG will be built from sources
OpenJPEG: VERSION = 2.4.0, BUILD = opencv-4.5.3-openjp2-2.4.0
OpenJPEG libraries will be built from sources: libopenjp2 (version "2.4.0")
Found TBB (cmake): D:/SDK/tbb/oneapi-tbb-2021.3.0-win/oneapi-tbb-2021.3.0/redist/ia32/vc14/tbb12.dll
found Intel IPP (ICV version): 2020.0.0 [2020.0.0 Gold]
at: D:/SDK/opencv/build/3rdparty/ippicv/ippicv_win/icv
found Intel IPP Integration Wrappers sources: 2020.0.0
at: D:/SDK/opencv/build/3rdparty/ippicv/ippicv_win/iw
Could not find OpenBLAS include. Turning OpenBLAS_FOUND off
Could not find OpenBLAS lib. Turning OpenBLAS_FOUND off
Could NOT find BLAS (missing: BLAS_LIBRARIES) 
LAPACK requires BLAS
A library with LAPACK API not found. Please specify library location.
Could NOT find JNI (missing: JAVA_AWT_LIBRARY JAVA_JVM_LIBRARY JAVA_INCLUDE_PATH JAVA_INCLUDE_PATH2 JAVA_AWT_INCLUDE_PATH) 
VTK is not found. Please set -DVTK_DIR in CMake to VTK build directory, or to VTK install subdirectory with VTKConfig.cmake file
Could NOT find PkgConfig (missing: PKG_CONFIG_EXECUTABLE) 
OpenCV Python: during development append to PYTHONPATH: D:/SDK/opencv/build/python_loader
Could NOT find PkgConfig (missing: PKG_CONFIG_EXECUTABLE) 
Processing WORLD modules...
    module opencv_core...
Allocator metrics storage type: 'long long'
    module opencv_flann...
    module opencv_imgproc...
    module opencv_ml...
    module opencv_photo...
    module opencv_dnn...
Registering hook 'INIT_MODULE_SOURCES_opencv_dnn': D:/SDK/opencv/opencv-4.5.3/modules/dnn/cmake/hooks/INIT_MODULE_SOURCES_opencv_dnn.cmake
opencv_dnn: filter out cuda4dnn source code
    module opencv_features2d...
    module opencv_imgcodecs...
    module opencv_videoio...
    module opencv_calib3d...
    module opencv_highgui...
highgui: using builtin backend: WIN32UI
    module opencv_objdetect...
    module opencv_stitching...
    module opencv_video...
    module opencv_gapi...
Processing WORLD modules... DONE
Excluding from source files list (optimization is disabled): modules/imgproc/src/corner.avx.cpp
Excluding from source files list (optimization is disabled): modules/imgproc/src/imgwarp.avx2.cpp
Excluding from source files list (optimization is disabled): modules/imgproc/src/imgwarp.sse4_1.cpp
Excluding from source files list (optimization is disabled): modules/imgproc/src/resize.avx2.cpp
Excluding from source files list (optimization is disabled): modules/imgproc/src/resize.sse4_1.cpp
Excluding from source files list (optimization is disabled): modules/features2d/src/fast.avx2.cpp

General configuration for OpenCV 4.5.3 =====================================
  Version control:               unknown

  Platform:
    Timestamp:                   2021-09-13T02:05:54Z
    Host:                        Windows 10.0.19042 AMD64
    CMake:                       3.15.0-rc1
    CMake generator:             Visual Studio 16 2019
    CMake build tool:            C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/MSBuild/Current/Bin/MSBuild.exe
    MSVC:                        1921
    Configuration:               Debug Release

  CPU/HW features:
    Baseline:                    SSE SSE2 SSE3 SSSE3 SSE4_1 POPCNT SSE4_2 FP16 FMA3 AVX AVX2
      requested:                 AVX2

  C/C++:
    Built as dynamic libs?:      YES
    C++ standard:                11
    C++ Compiler:                C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.21.27702/bin/Hostx64/x86/cl.exe  (ver 19.21.27702.2)
    C++ flags (Release):         /DWIN32 /D_WINDOWS /W4 /GR  /D _CRT_SECURE_NO_DEPRECATE /D _CRT_NONSTDC_NO_DEPRECATE /D _SCL_SECURE_NO_WARNINGS /Gy /bigobj /Oi  /fp:precise  /arch:SSE /arch:SSE2      /arch:AVX  /arch:AVX /arch:AVX2 /EHa /wd4127 /wd4251 /wd4324 /wd4275 /wd4512 /wd4589 /MP  /MD /O2 /Ob2 /DNDEBUG 
    C++ flags (Debug):           /DWIN32 /D_WINDOWS /W4 /GR  /D _CRT_SECURE_NO_DEPRECATE /D _CRT_NONSTDC_NO_DEPRECATE /D _SCL_SECURE_NO_WARNINGS /Gy /bigobj /Oi  /fp:precise  /arch:SSE /arch:SSE2      /arch:AVX  /arch:AVX /arch:AVX2 /EHa /wd4127 /wd4251 /wd4324 /wd4275 /wd4512 /wd4589 /MP  /MDd /Zi /Ob0 /Od /RTC1 
    C Compiler:                  C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.21.27702/bin/Hostx64/x86/cl.exe
    C flags (Release):           /DWIN32 /D_WINDOWS /W3  /D _CRT_SECURE_NO_DEPRECATE /D _CRT_NONSTDC_NO_DEPRECATE /D _SCL_SECURE_NO_WARNINGS /Gy /bigobj /Oi  /fp:precise  /arch:SSE /arch:SSE2      /arch:AVX  /arch:AVX /arch:AVX2 /MP   /MD /O2 /Ob2 /DNDEBUG 
    C flags (Debug):             /DWIN32 /D_WINDOWS /W3  /D _CRT_SECURE_NO_DEPRECATE /D _CRT_NONSTDC_NO_DEPRECATE /D _SCL_SECURE_NO_WARNINGS /Gy /bigobj /Oi  /fp:precise  /arch:SSE /arch:SSE2      /arch:AVX  /arch:AVX /arch:AVX2 /MP /MDd /Zi /Ob0 /Od /RTC1 
    Linker flags (Release):      /machine:X86  /INCREMENTAL:NO 
    Linker flags (Debug):        /machine:X86  /debug /INCREMENTAL 
    ccache:                      NO
    Precompiled headers:         NO
    Extra dependencies:
    3rdparty dependencies:

  OpenCV modules:
    To be built:                 calib3d core dnn features2d flann gapi highgui imgcodecs imgproc ml objdetect photo stitching ts video videoio world
    Disabled:                    -
    Disabled by dependency:      -
    Unavailable:                 java python2 python3
    Applications:                tests perf_tests apps
    Documentation:               NO
    Non-free algorithms:         NO

  Windows RT support:            NO

  GUI: 
    Win32 UI:                    YES
    VTK support:                 NO

  Media I/O: 
    ZLib:                        build (ver 1.2.11)
    JPEG:                        build-libjpeg-turbo (ver 2.1.0-62)
    WEBP:                        build (ver encoder: 0x020f)
    PNG:                         build (ver 1.6.37)
    TIFF:                        build (ver 42 - 4.2.0)
    JPEG 2000:                   build (ver 2.4.0)
    OpenEXR:                     build (ver 2.3.0)
    HDR:                         YES
    SUNRASTER:                   YES
    PXM:                         YES
    PFM:                         YES

  Video I/O:
    DC1394:                      NO
    FFMPEG:                      YES (prebuilt binaries)
      avcodec:                   YES (58.134.100)
      avformat:                  YES (58.76.100)
      avutil:                    YES (56.70.100)
      swscale:                   YES (5.9.100)
      avresample:                YES (4.0.0)
    GStreamer:                   NO
    DirectShow:                  YES
    Media Foundation:            YES
      DXVA:                      YES

  Parallel framework:            TBB (ver 2021.3 interface 12030)

  Trace:                         YES (with Intel ITT)

  Other third-party libraries:
    Intel IPP:                   2020.0.0 Gold [2020.0.0]
           at:                   D:/SDK/opencv/build/3rdparty/ippicv/ippicv_win/icv
    Intel IPP IW:                sources (2020.0.0)
              at:                D:/SDK/opencv/build/3rdparty/ippicv/ippicv_win/iw
    Lapack:                      NO
    Eigen:                       NO
    Custom HAL:                  NO
    Protobuf:                    build (3.5.1)

  OpenCL:                        YES (NVD3D11)
    Include path:                D:/SDK/opencv/opencv-4.5.3/3rdparty/include/opencl/1.2
    Link libraries:              Dynamic load

  Python (for build):            C:/Users/haomi/AppData/Local/Programs/Python/Python37/python.exe

  Java:                          
    ant:                         NO
    JNI:                         NO
    Java wrappers:               NO
    Java tests:                  NO

  Install to:                    D:/SDK/opencv/build/install
-----------------------------------------------------------------

Configuring done
Generating done
```

## 下载

- 蓝奏云：
地址: [https://mangoroom.lanzoui.com/ilj6utyn9yj](https://mangoroom.lanzoui.com/ilj6utyn9yj )
密码:a9im
- onedirve
地址: [https://emailszueducn-my.sharepoint.com/:u:/g/personal/2015130324_email_szu_edu_cn/ESNxqR0HVhVLhv-OJLxCrD4B8dsCIM52-toYAX4iWSPXNA?e=Ar3Ffa](https://emailszueducn-my.sharepoint.com/:u:/g/personal/2015130324_email_szu_edu_cn/ESNxqR0HVhVLhv-OJLxCrD4B8dsCIM52-toYAX4iWSPXNA?e=Ar3Ffa)
密码: mango

------

本文由芒果浩明发布，转载请注明出处。
本文链接：https://blog.mangoroom.cn/shared/opencv-4.5.3-x86-vc16-shared-avx2-world-tbb.html

