---
title: "opencv官方samples系列3-samples文件目录树"
categories: [ "opencv" ]
tags: [ "opencv","图像处理","计算机视觉","opencv-samples","powershell" ]
draft: false
slug: "opencv-sample3-files-tree"
date: "2019-05-19 13:04:00"
---

## 序

芒果本想着在正式开始学习opencv的samples之前先弄个学习目录或者计划之类的，这样方便看到学习的进度。也相当于一个打卡表吧，但文件列表过多，手工一个一个填写制作目录太麻烦了。于是芒果直接使用powershell的命令行一键输出，输出了例子的文件结构。这个命令也非常地简单，在对应的文件夹指向按住shift+鼠标右键，打开powershell,输入以下命令

```powershell
tree /f
```

## 目录
```powershell

PS F:\Code\opencv-master\samples> tree /f
Folder PATH listing
Volume serial number is 0D61-1601
F:.
│  CMakeLists.example.in
│  CMakeLists.txt
│  samples_utils.cmake
│  _winpack_build_sample.cmd
│  _winpack_run_python_sample.cmd
│
├─android
│  │  build.gradle.in
│  │  CMakeLists.txt
│  │
│  ├─15-puzzle
│  │  │  .classpath
│  │  │  .project
│  │  │  AndroidManifest.xml
│  │  │  build.gradle.in
│  │  │  CMakeLists.txt
│  │  │
│  │  ├─.settings
│  │  │      org.eclipse.jdt.core.prefs
│  │  │
│  │  ├─gradle
│  │  │      AndroidManifest.xml
│  │  │
│  │  ├─res
│  │  │  ├─drawable
│  │  │  │      icon.png
│  │  │  │
│  │  │  └─values
│  │  │          strings.xml
│  │  │
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─puzzle15
│  │                          Puzzle15Activity.java
│  │                          Puzzle15Processor.java
│  │
│  ├─camera-calibration
│  │  │  .classpath
│  │  │  .project
│  │  │  AndroidManifest.xml
│  │  │  build.gradle.in
│  │  │  CMakeLists.txt
│  │  │
│  │  ├─.settings
│  │  │      org.eclipse.jdt.core.prefs
│  │  │
│  │  ├─gradle
│  │  │      AndroidManifest.xml
│  │  │
│  │  ├─res
│  │  │  ├─drawable
│  │  │  │      icon.png
│  │  │  │
│  │  │  ├─layout
│  │  │  │      camera_calibration_surface_view.xml
│  │  │  │
│  │  │  ├─menu
│  │  │  │      calibration.xml
│  │  │  │
│  │  │  └─values
│  │  │          strings.xml
│  │  │
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─cameracalibration
│  │                          CalibrationResult.java
│  │                          CameraCalibrationActivity.java
│  │                          CameraCalibrator.java
│  │                          OnCameraFrameRender.java
│  │
│  ├─color-blob-detection
│  │  │  .classpath
│  │  │  .project
│  │  │  AndroidManifest.xml
│  │  │  build.gradle.in
│  │  │  CMakeLists.txt
│  │  │
│  │  ├─.settings
│  │  │      org.eclipse.jdt.core.prefs
│  │  │
│  │  ├─gradle
│  │  │      AndroidManifest.xml
│  │  │
│  │  ├─res
│  │  │  ├─drawable
│  │  │  │      icon.png
│  │  │  │
│  │  │  ├─layout
│  │  │  │      color_blob_detection_surface_view.xml
│  │  │  │
│  │  │  └─values
│  │  │          strings.xml
│  │  │
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─colorblobdetect
│  │                          ColorBlobDetectionActivity.java
│  │                          ColorBlobDetector.java
│  │
│  ├─face-detection
│  │  │  .classpath
│  │  │  .cproject
│  │  │  .project
│  │  │  AndroidManifest.xml
│  │  │  build.gradle.in
│  │  │  CMakeLists.txt
│  │  │
│  │  ├─.settings
│  │  │      org.eclipse.jdt.core.prefs
│  │  │
│  │  ├─gradle
│  │  │      AndroidManifest.xml
│  │  │
│  │  ├─jni
│  │  │      Android.mk
│  │  │      Application.mk
│  │  │      CMakeLists.txt
│  │  │      DetectionBasedTracker_jni.cpp
│  │  │      DetectionBasedTracker_jni.h
│  │  │
│  │  ├─res
│  │  │  ├─drawable
│  │  │  │      icon.png
│  │  │  │
│  │  │  ├─layout
│  │  │  │      face_detect_surface_view.xml
│  │  │  │
│  │  │  ├─raw
│  │  │  │      lbpcascade_frontalface.xml
│  │  │  │
│  │  │  └─values
│  │  │          strings.xml
│  │  │
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─facedetect
│  │                          DetectionBasedTracker.java
│  │                          FdActivity.java
│  │
│  ├─image-manipulations
│  │  │  .classpath
│  │  │  .project
│  │  │  AndroidManifest.xml
│  │  │  build.gradle.in
│  │  │  CMakeLists.txt
│  │  │
│  │  ├─.settings
│  │  │      org.eclipse.jdt.core.prefs
│  │  │
│  │  ├─gradle
│  │  │      AndroidManifest.xml
│  │  │
│  │  ├─res
│  │  │  ├─drawable
│  │  │  │      icon.png
│  │  │  │
│  │  │  ├─layout
│  │  │  │      image_manipulations_surface_view.xml
│  │  │  │
│  │  │  └─values
│  │  │          strings.xml
│  │  │
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─imagemanipulations
│  │                          ImageManipulationsActivity.java
│  │
│  ├─mobilenet-objdetect
│  │  │  AndroidManifest.xml
│  │  │  build.gradle.in
│  │  │  CMakeLists.txt
│  │  │
│  │  ├─gradle
│  │  │      AndroidManifest.xml
│  │  │
│  │  ├─res
│  │  │  ├─layout
│  │  │  │      activity_main.xml
│  │  │  │
│  │  │  └─values
│  │  │          strings.xml
│  │  │
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─opencv_mobilenet
│  │                          MainActivity.java
│  │
│  ├─tutorial-1-camerapreview
│  │  │  .classpath
│  │  │  .project
│  │  │  AndroidManifest.xml
│  │  │  build.gradle.in
│  │  │  CMakeLists.txt
│  │  │
│  │  ├─.settings
│  │  │      org.eclipse.jdt.core.prefs
│  │  │
│  │  ├─gradle
│  │  │      AndroidManifest.xml
│  │  │
│  │  ├─res
│  │  │  ├─drawable
│  │  │  │      icon.png
│  │  │  │
│  │  │  ├─layout
│  │  │  │      tutorial1_surface_view.xml
│  │  │  │
│  │  │  └─values
│  │  │          strings.xml
│  │  │
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─tutorial1
│  │                          Tutorial1Activity.java
│  │
│  ├─tutorial-2-mixedprocessing
│  │  │  .classpath
│  │  │  .cproject
│  │  │  .project
│  │  │  AndroidManifest.xml
│  │  │  build.gradle.in
│  │  │  CMakeLists.txt
│  │  │
│  │  ├─.settings
│  │  │      org.eclipse.jdt.core.prefs
│  │  │
│  │  ├─gradle
│  │  │      AndroidManifest.xml
│  │  │
│  │  ├─jni
│  │  │      Android.mk
│  │  │      Application.mk
│  │  │      CMakeLists.txt
│  │  │      jni_part.cpp
│  │  │
│  │  ├─res
│  │  │  ├─drawable
│  │  │  │      icon.png
│  │  │  │
│  │  │  ├─layout
│  │  │  │      tutorial2_surface_view.xml
│  │  │  │
│  │  │  └─values
│  │  │          strings.xml
│  │  │
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─tutorial2
│  │                          Tutorial2Activity.java
│  │
│  ├─tutorial-3-cameracontrol
│  │  │  .classpath
│  │  │  .project
│  │  │  AndroidManifest.xml
│  │  │  build.gradle.in
│  │  │  CMakeLists.txt
│  │  │
│  │  ├─.settings
│  │  │      org.eclipse.jdt.core.prefs
│  │  │
│  │  ├─gradle
│  │  │      AndroidManifest.xml
│  │  │
│  │  ├─res
│  │  │  ├─drawable
│  │  │  │      icon.png
│  │  │  │
│  │  │  ├─layout
│  │  │  │      tutorial3_surface_view.xml
│  │  │  │
│  │  │  └─values
│  │  │          strings.xml
│  │  │
│  │  └─src
│  │      └─org
│  │          └─opencv
│  │              └─samples
│  │                  └─tutorial3
│  │                          Tutorial3Activity.java
│  │                          Tutorial3View.java
│  │
│  └─tutorial-4-opencl
│      │  .classpath
│      │  .cproject
│      │  .project
│      │  AndroidManifest.xml
│      │  build.gradle.in
│      │  CMakeLists.txt
│      │  lint.xml
│      │
│      ├─.settings
│      │      org.eclipse.jdt.core.prefs
│      │
│      ├─gradle
│      │      AndroidManifest.xml
│      │
│      ├─jni
│      │      Android.mk
│      │      Application.mk
│      │      CLprocessor.cpp
│      │      CMakeLists.txt
│      │      common.hpp
│      │      jni.c
│      │
│      ├─res
│      │  ├─drawable
│      │  │      icon.png
│      │  │
│      │  ├─layout
│      │  │      activity.xml
│      │  │
│      │  ├─menu
│      │  │      menu.xml
│      │  │
│      │  └─values
│      │          strings.xml
│      │
│      └─src
│          └─org
│              └─opencv
│                  └─samples
│                      └─tutorial4
│                              MyGLSurfaceView.java
│                              NativePart.java
│                              Tutorial4Activity.java
│
├─cpp
│  │  3calibration.cpp
│  │  application_trace.cpp
│  │  bgfg_segm.cpp
│  │  calibration.cpp
│  │  camshiftdemo.cpp
│  │  cloning_demo.cpp
│  │  cloning_gui.cpp
│  │  CMakeLists.txt
│  │  connected_components.cpp
│  │  contours2.cpp
│  │  convexhull.cpp
│  │  cout_mat.cpp
│  │  create_mask.cpp
│  │  dbt_face_detection.cpp
│  │  delaunay2.cpp
│  │  demhist.cpp
│  │  detect_blob.cpp
│  │  detect_mser.cpp
│  │  dft.cpp
│  │  digits.cpp
│  │  distrans.cpp
│  │  dis_opticalflow.cpp
│  │  drawing.cpp
│  │  edge.cpp
│  │  ela.cpp
│  │  em.cpp
│  │  facedetect.cpp
│  │  facial_features.cpp
│  │  falsecolor.cpp
│  │  fback.cpp
│  │  ffilldemo.cpp
│  │  filestorage.cpp
│  │  fitellipse.cpp
│  │  grabcut.cpp
│  │  imagelist_creator.cpp
│  │  imagelist_reader.cpp
│  │  image_alignment.cpp
│  │  inpaint.cpp
│  │  kalman.cpp
│  │  kmeans.cpp
│  │  laplace.cpp
│  │  letter_recog.cpp
│  │  lkdemo.cpp
│  │  logistic_regression.cpp
│  │  mask_tmpl.cpp
│  │  matchmethod_orb_akaze_brisk.cpp
│  │  minarea.cpp
│  │  morphology2.cpp
│  │  neural_network.cpp
│  │  npr_demo.cpp
│  │  opencv_version.cpp
│  │  pca.cpp
│  │  peopledetect.cpp
│  │  phase_corr.cpp
│  │  points_classifier.cpp
│  │  polar_transforms.cpp
│  │  qrcode.cpp
│  │  segment_objects.cpp
│  │  select3dobj.cpp
│  │  smiledetect.cpp
│  │  squares.cpp
│  │  stereo_calib.cpp
│  │  stereo_match.cpp
│  │  stitching.cpp
│  │  stitching_detailed.cpp
│  │  train_HOG.cpp
│  │  train_svmsgd.cpp
│  │  travelsalesman.cpp
│  │  tree_engine.cpp
│  │  videocapture_basic.cpp
│  │  videocapture_camera.cpp
│  │  videocapture_gphoto2_autofocus.cpp
│  │  videocapture_gstreamer_pipeline.cpp
│  │  videocapture_image_sequence.cpp
│  │  videocapture_intelperc.cpp
│  │  videocapture_openni.cpp
│  │  videocapture_starter.cpp
│  │  videowriter_basic.cpp
│  │  warpPerspective_demo.cpp
│  │  watershed.cpp
│  │
│  ├─example_cmake
│  │      CMakeLists.txt
│  │      example.cpp
│  │      Makefile
│  │
│  └─tutorial_code
│      ├─calib3d
│      │  ├─camera_calibration
│      │  │      camera_calibration.cpp
│      │  │      in_VID5.xml
│      │  │      out_camera_data.yml
│      │  │      VID5.xml
│      │  │
│      │  └─real_time_pose_estimation
│      │      │  CMakeLists.txt
│      │      │
│      │      ├─Data
│      │      │      box.mp4
│      │      │      box.ply
│      │      │      cookies_ORB.yml
│      │      │      resized_IMG_3875.JPG
│      │      │
│      │      └─src
│      │              CsvReader.cpp
│      │              CsvReader.h
│      │              CsvWriter.cpp
│      │              CsvWriter.h
│      │              main_detection.cpp
│      │              main_registration.cpp
│      │              Mesh.cpp
│      │              Mesh.h
│      │              Model.cpp
│      │              Model.h
│      │              ModelRegistration.cpp
│      │              ModelRegistration.h
│      │              PnPProblem.cpp
│      │              PnPProblem.h
│      │              RobustMatcher.cpp
│      │              RobustMatcher.h
│      │              Utils.cpp
│      │              Utils.h
│      │
│      ├─compatibility
│      │      compatibility_test.cpp
│      │
│      ├─core
│      │  ├─AddingImages
│      │  │      AddingImages.cpp
│      │  │
│      │  ├─discrete_fourier_transform
│      │  │      discrete_fourier_transform.cpp
│      │  │
│      │  ├─file_input_output
│      │  │      file_input_output.cpp
│      │  │
│      │  ├─how_to_scan_images
│      │  │      how_to_scan_images.cpp
│      │  │
│      │  ├─how_to_use_OpenCV_parallel_for_
│      │  │      how_to_use_OpenCV_parallel_for_.cpp
│      │  │
│      │  ├─mat_mask_operations
│      │  │      mat_mask_operations.cpp
│      │  │
│      │  ├─mat_operations
│      │  │      mat_operations.cpp
│      │  │
│      │  └─mat_the_basic_image_container
│      │          mat_the_basic_image_container.cpp
│      │
│      ├─features2D
│      │  │  AKAZE_match.cpp
│      │  │
│      │  ├─AKAZE_tracking
│      │  │      planar_tracking.cpp
│      │  │      stats.h
│      │  │      utils.h
│      │  │
│      │  ├─feature_description
│      │  │      SURF_matching_Demo.cpp
│      │  │
│      │  ├─feature_detection
│      │  │      SURF_detection_Demo.cpp
│      │  │
│      │  ├─feature_flann_matcher
│      │  │      SURF_FLANN_matching_Demo.cpp
│      │  │
│      │  ├─feature_homography
│      │  │      SURF_FLANN_matching_homography_Demo.cpp
│      │  │
│      │  └─Homography
│      │          decompose_homography.cpp
│      │          homography_from_camera_displacement.cpp
│      │          panorama_stitching_rotating_camera.cpp
│      │          perspective_correction.cpp
│      │          pose_from_homography.cpp
│      │
│      ├─gapi
│      │  └─porting_anisotropic_image_segmentation
│      │          porting_anisotropic_image_segmentation_gapi.cpp
│      │          porting_anisotropic_image_segmentation_gapi_fluid.cpp
│      │
│      ├─gpu
│      │  ├─gpu-basics-similarity
│      │  │      gpu-basics-similarity.cpp
│      │  │
│      │  └─gpu-thrust-interop
│      │          CMakeLists.txt
│      │          main.cu
│      │          Thrust_interop.hpp
│      │
│      ├─HighGUI
│      │      AddingImagesTrackbar.cpp
│      │      BasicLinearTransformsTrackbar.cpp
│      │
│      ├─Histograms_Matching
│      │      calcBackProject_Demo1.cpp
│      │      calcBackProject_Demo2.cpp
│      │      calcHist_Demo.cpp
│      │      compareHist_Demo.cpp
│      │      EqualizeHist_Demo.cpp
│      │      MatchTemplate_Demo.cpp
│      │
│      ├─imgcodecs
│      │  └─GDAL_IO
│      │          gdal-image.cpp
│      │
│      ├─ImgProc
│      │  │  BasicLinearTransforms.cpp
│      │  │  Morphology_1.cpp
│      │  │  Morphology_2.cpp
│      │  │  Threshold.cpp
│      │  │  Threshold_inRange.cpp
│      │  │
│      │  ├─anisotropic_image_segmentation
│      │  │      anisotropic_image_segmentation.cpp
│      │  │
│      │  ├─basic_drawing
│      │  │      Drawing_1.cpp
│      │  │      Drawing_2.cpp
│      │  │
│      │  ├─changing_contrast_brightness_image
│      │  │      changing_contrast_brightness_image.cpp
│      │  │
│      │  ├─HitMiss
│      │  │      HitMiss.cpp
│      │  │
│      │  ├─morph_lines_detection
│      │  │      Morphology_3.cpp
│      │  │
│      │  ├─motion_deblur_filter
│      │  │      motion_deblur_filter.cpp
│      │  │
│      │  ├─out_of_focus_deblur_filter
│      │  │      out_of_focus_deblur_filter.cpp
│      │  │
│      │  ├─periodic_noise_removing_filter
│      │  │      periodic_noise_removing_filter.cpp
│      │  │
│      │  ├─Pyramids
│      │  │      Pyramids.cpp
│      │  │
│      │  └─Smoothing
│      │          Smoothing.cpp
│      │
│      ├─ImgTrans
│      │      CannyDetector_Demo.cpp
│      │      copyMakeBorder_demo.cpp
│      │      filter2D_demo.cpp
│      │      Geometric_Transforms_Demo.cpp
│      │      houghcircles.cpp
│      │      HoughCircle_Demo.cpp
│      │      houghlines.cpp
│      │      HoughLines_Demo.cpp
│      │      imageSegmentation.cpp
│      │      Laplace_Demo.cpp
│      │      Remap_Demo.cpp
│      │      Sobel_Demo.cpp
│      │
│      ├─introduction
│      │  ├─display_image
│      │  │      display_image.cpp
│      │  │
│      │  ├─documentation
│      │  │      documentation.cpp
│      │  │
│      │  └─windows_visual_studio_opencv
│      │          introduction_windows_vs.cpp
│      │
│      ├─ml
│      │  ├─introduction_to_pca
│      │  │      introduction_to_pca.cpp
│      │  │
│      │  ├─introduction_to_svm
│      │  │      introduction_to_svm.cpp
│      │  │
│      │  └─non_linear_svms
│      │          non_linear_svms.cpp
│      │
│      ├─objectDetection
│      │      objectDetection.cpp
│      │
│      ├─photo
│      │  ├─decolorization
│      │  │      decolor.cpp
│      │  │
│      │  ├─hdr_imaging
│      │  │      hdr_imaging.cpp
│      │  │
│      │  ├─non_photorealistic_rendering
│      │  │      npr_demo.cpp
│      │  │
│      │  └─seamless_cloning
│      │          cloning_demo.cpp
│      │          cloning_gui.cpp
│      │
│      ├─ShapeDescriptors
│      │      findContours_demo.cpp
│      │      generalContours_demo1.cpp
│      │      generalContours_demo2.cpp
│      │      hull_demo.cpp
│      │      moments_demo.cpp
│      │      pointPolygonTest_demo.cpp
│      │
│      ├─snippets
│      │      core_mat_checkVector.cpp
│      │      core_merge.cpp
│      │      core_reduce.cpp
│      │      core_split.cpp
│      │      core_various.cpp
│      │      imgcodecs_imwrite.cpp
│      │      imgproc_applyColorMap.cpp
│      │      imgproc_calcHist.cpp
│      │      imgproc_drawContours.cpp
│      │      imgproc_HoughLinesCircles.cpp
│      │      imgproc_HoughLinesP.cpp
│      │      imgproc_HoughLinesPointSet.cpp
│      │
│      ├─TrackingMotion
│      │      cornerDetector_Demo.cpp
│      │      cornerHarris_Demo.cpp
│      │      cornerSubPix_Demo.cpp
│      │      goodFeaturesToTrack_Demo.cpp
│      │
│      ├─video
│      │  │  bg_sub.cpp
│      │  │
│      │  ├─meanshift
│      │  │      camshift.cpp
│      │  │      meanshift.cpp
│      │  │
│      │  └─optical_flow
│      │          optical_flow.cpp
│      │          optical_flow_dense.cpp
│      │
│      ├─videoio
│      │  ├─video-input-psnr-ssim
│      │  │      video-input-psnr-ssim.cpp
│      │  │
│      │  └─video-write
│      │          video-write.cpp
│      │
│      └─xfeatures2D
│              LATCH_match.cpp
│
├─data
│  │  aero1.jpg
│  │  aero3.jpg
│  │  aloeGT.png
│  │  aloeL.jpg
│  │  aloeR.jpg
│  │  apple.jpg
│  │  baboon.jpg
│  │  basketball1.png
│  │  basketball2.png
│  │  Blender_Suzanne1.jpg
│  │  Blender_Suzanne2.jpg
│  │  blox.jpg
│  │  board.jpg
│  │  box.png
│  │  box_in_scene.png
│  │  building.jpg
│  │  butterfly.jpg
│  │  calibration.yml
│  │  cards.png
│  │  chessboard.png
│  │  chicky_512.png
│  │  data01.xml
│  │  detect_blob.png
│  │  digits.png
│  │  ela_modified.jpg
│  │  ela_original.jpg
│  │  ellipses.jpg
│  │  fruits.jpg
│  │  gradient.png
│  │  graf1.png
│  │  graf3.png
│  │  H1to3p.xml
│  │  HappyFish.jpg
│  │  home.jpg
│  │  imageTextN.png
│  │  imageTextR.png
│  │  intrinsics.yml
│  │  left.jpg
│  │  left01.jpg
│  │  left02.jpg
│  │  left03.jpg
│  │  left04.jpg
│  │  left05.jpg
│  │  left06.jpg
│  │  left07.jpg
│  │  left08.jpg
│  │  left09.jpg
│  │  left11.jpg
│  │  left12.jpg
│  │  left13.jpg
│  │  left14.jpg
│  │  left_intrinsics.yml
│  │  lena.jpg
│  │  lena_tmpl.jpg
│  │  letter-recognition.data
│  │  licenseplate_motion.jpg
│  │  LinuxLogo.jpg
│  │  mask.png
│  │  Megamind.avi
│  │  Megamind_bugy.avi
│  │  messi5.jpg
│  │  ml.png
│  │  notes.png
│  │  opencv-logo-white.png
│  │  opencv-logo.png
│  │  orange.jpg
│  │  pca_test1.jpg
│  │  pic1.png
│  │  pic2.png
│  │  pic3.png
│  │  pic4.png
│  │  pic5.png
│  │  pic6.png
│  │  right.jpg
│  │  right01.jpg
│  │  right02.jpg
│  │  right03.jpg
│  │  right04.jpg
│  │  right05.jpg
│  │  right06.jpg
│  │  right07.jpg
│  │  right08.jpg
│  │  right09.jpg
│  │  right11.jpg
│  │  right12.jpg
│  │  right13.jpg
│  │  right14.jpg
│  │  rubberwhale1.png
│  │  rubberwhale2.png
│  │  smarties.png
│  │  starry_night.jpg
│  │  stereo_calib.xml
│  │  stuff.jpg
│  │  sudoku.png
│  │  templ.png
│  │  text_defocus.jpg
│  │  text_motion.jpg
│  │  tmpl.png
│  │  tree.avi
│  │  vtest.avi
│  │  WindowsLogo.jpg
│  │
│  └─dnn
│          classification_classes_ILSVRC2012.txt
│          enet-classes.txt
│          object_detection_classes_coco.txt
│          object_detection_classes_pascal_voc.txt
│          object_detection_classes_yolov3.txt
│
├─directx
│      CMakeLists.txt
│      d3d10_interop.cpp
│      d3d11_interop.cpp
│      d3d9ex_interop.cpp
│      d3d9_interop.cpp
│      d3dsample.hpp
│      winapp.hpp
│
├─dnn
│  │  classification.cpp
│  │  classification.py
│  │  CMakeLists.txt
│  │  colorization.cpp
│  │  colorization.py
│  │  common.hpp
│  │  common.py
│  │  custom_layers.hpp
│  │  edge_detection.py
│  │  fast_neural_style.py
│  │  js_face_recognition.html
│  │  mask_rcnn.py
│  │  mobilenet_ssd_accuracy.py
│  │  models.yml
│  │  object_detection.cpp
│  │  object_detection.py
│  │  openpose.cpp
│  │  openpose.py
│  │  README.md
│  │  segmentation.cpp
│  │  segmentation.py
│  │  shrink_tf_graph_weights.py
│  │  text_detection.cpp
│  │  text_detection.py
│  │  tf_text_graph_common.py
│  │  tf_text_graph_faster_rcnn.py
│  │  tf_text_graph_mask_rcnn.py
│  │  tf_text_graph_ssd.py
│  │
│  └─face_detector
│          deploy.prototxt
│          download_weights.py
│          how_to_train_face_detector.txt
│          opencv_face_detector.pbtxt
│          solver.prototxt
│          test.prototxt
│          train.prototxt
│          weights.meta4
│
├─gpu
│      alpha_comp.cpp
│      bgfg_segm.cpp
│      cascadeclassifier.cpp
│      CMakeLists.txt
│      farneback_optical_flow.cpp
│      generalized_hough.cpp
│      hog.cpp
│      houghlines.cpp
│      morphology.cpp
│      multi.cpp
│      optical_flow.cpp
│      pyrlk_optical_flow.cpp
│      stereo_match.cpp
│      stereo_multi.cpp
│      super_resolution.cpp
│      surf_keypoint_matcher.cpp
│      video_reader.cpp
│      video_writer.cpp
│
├─hal
│  │  README.md
│  │
│  ├─c_hal
│  │      CMakeLists.txt
│  │      config.cmake
│  │      impl.c
│  │      impl.h
│  │
│  └─slow_hal
│          CMakeLists.txt
│          config.cmake
│          impl.cpp
│          impl.hpp
│
├─java
│  │  opencv_version.java
│  │
│  ├─ant
│  │  │  build.xml
│  │  │
│  │  └─src
│  │          SimpleSample.java
│  │
│  ├─clojure
│  │  └─simple-sample
│  │      │  project.clj
│  │      │
│  │      ├─resources
│  │      │  └─images
│  │      │          lena.png
│  │      │
│  │      ├─src
│  │      │  └─simple_sample
│  │      │          core.clj
│  │      │
│  │      └─test
│  │          └─simple_sample
│  │                  core_test.clj
│  │
│  ├─eclipse
│  │  └─HelloCV
│  │      │  .classpath
│  │      │  .project
│  │      │
│  │      ├─.settings
│  │      │      org.eclipse.jdt.core.prefs
│  │      │
│  │      └─src
│  │              Main.java
│  │
│  ├─sbt
│  │  │  README
│  │  │
│  │  ├─lib
│  │  │      copy_opencv_jar_here
│  │  │
│  │  ├─project
│  │  │      build.scala
│  │  │      plugins.sbt
│  │  │
│  │  ├─sbt
│  │  │      sbt
│  │  │      sbt-launch.jar
│  │  │
│  │  └─src
│  │      └─main
│  │          ├─java
│  │          │      DetectFaceDemo.java
│  │          │
│  │          ├─resources
│  │          │      AverageMaleFace.jpg
│  │          │      img1.png
│  │          │      img2.png
│  │          │
│  │          └─scala
│  │                  Main.scala
│  │                  ScalaCorrespondenceMatchingDemo.scala
│  │                  ScalaDetectFaceDemo.scala
│  │
│  └─tutorial_code
│      │  build.xml
│      │  CMakeLists.txt
│      │
│      ├─core
│      │  ├─AddingImages
│      │  │      AddingImages.java
│      │  │
│      │  ├─discrete_fourier_transform
│      │  │      DiscreteFourierTransform.java
│      │  │
│      │  ├─mat_mask_operations
│      │  │      MatMaskOperations.java
│      │  │
│      │  └─mat_operations
│      │          MatOperations.java
│      │
│      ├─features2D
│      │  ├─akaze_matching
│      │  │      AKAZEMatchDemo.java
│      │  │
│      │  ├─feature_description
│      │  │      SURFMatchingDemo.java
│      │  │
│      │  ├─feature_detection
│      │  │      SURFDetectionDemo.java
│      │  │
│      │  ├─feature_flann_matcher
│      │  │      SURFFLANNMatchingDemo.java
│      │  │
│      │  └─feature_homography
│      │          SURFFLANNMatchingHomographyDemo.java
│      │
│      ├─highgui
│      │  └─trackbar
│      │          AddingImagesTrackbar.java
│      │
│      ├─Histograms_Matching
│      │  ├─back_projection
│      │  │      CalcBackProjectDemo1.java
│      │  │      CalcBackProjectDemo2.java
│      │  │
│      │  ├─histogram_calculation
│      │  │      CalcHistDemo.java
│      │  │
│      │  ├─histogram_comparison
│      │  │      CompareHistDemo.java
│      │  │
│      │  └─histogram_equalization
│      │          EqualizeHistDemo.java
│      │
│      ├─ImgProc
│      │  ├─BasicGeometricDrawing
│      │  │      BasicGeometricDrawing.java
│      │  │
│      │  ├─changing_contrast_brightness_image
│      │  │      BasicLinearTransformsDemo.java
│      │  │      ChangingContrastBrightnessImageDemo.java
│      │  │
│      │  ├─erosion_dilatation
│      │  │      MorphologyDemo1.java
│      │  │
│      │  ├─HitMiss
│      │  │      HitMiss.java
│      │  │
│      │  ├─morph_lines_detection
│      │  │      Morphology_3.java
│      │  │
│      │  ├─opening_closing_hats
│      │  │      MorphologyDemo2.java
│      │  │
│      │  ├─Pyramids
│      │  │      Pyramids.java
│      │  │
│      │  ├─Smoothing
│      │  │      Smoothing.java
│      │  │
│      │  ├─threshold
│      │  │      Threshold.java
│      │  │
│      │  ├─threshold_inRange
│      │  │      ThresholdInRange.java
│      │  │
│      │  └─tutorial_template_matching
│      │          MatchTemplateDemo.java
│      │
│      ├─ImgTrans
│      │  ├─canny_detector
│      │  │      CannyDetectorDemo.java
│      │  │
│      │  ├─distance_transformation
│      │  │      ImageSegmentationDemo.java
│      │  │
│      │  ├─Filter2D
│      │  │      Filter2D_Demo.java
│      │  │
│      │  ├─HoughCircle
│      │  │      HoughCircles.java
│      │  │
│      │  ├─HoughLine
│      │  │      HoughLines.java
│      │  │
│      │  ├─LaPlace
│      │  │      LaplaceDemo.java
│      │  │
│      │  ├─MakeBorder
│      │  │      CopyMakeBorder.java
│      │  │
│      │  ├─remap
│      │  │      RemapDemo.java
│      │  │
│      │  ├─SobelDemo
│      │  │      SobelDemo.java
│      │  │
│      │  └─warp_affine
│      │          GeometricTransformsDemo.java
│      │
│      ├─introduction
│      │  └─documentation
│      │          Documentation.java
│      │
│      ├─ml
│      │  ├─introduction_to_pca
│      │  │      IntroductionToPCADemo.java
│      │  │
│      │  ├─introduction_to_svm
│      │  │      IntroductionToSVMDemo.java
│      │  │
│      │  └─non_linear_svms
│      │          NonLinearSVMsDemo.java
│      │
│      ├─objectDetection
│      │  └─cascade_classifier
│      │          ObjectDetectionDemo.java
│      │
│      ├─photo
│      │  └─hdr_imaging
│      │          HDRImagingDemo.java
│      │
│      ├─ShapeDescriptors
│      │  ├─bounding_rects_circles
│      │  │      GeneralContoursDemo1.java
│      │  │
│      │  ├─bounding_rotated_ellipses
│      │  │      GeneralContoursDemo2.java
│      │  │
│      │  ├─find_contours
│      │  │      FindContoursDemo.java
│      │  │
│      │  ├─hull
│      │  │      HullDemo.java
│      │  │
│      │  ├─moments
│      │  │      MomentsDemo.java
│      │  │
│      │  └─point_polygon_test
│      │          PointPolygonTestDemo.java
│      │
│      ├─TrackingMotion
│      │  ├─corner_subpixels
│      │  │      CornerSubPixDemo.java
│      │  │
│      │  ├─generic_corner_detector
│      │  │      CornerDetectorDemo.java
│      │  │
│      │  ├─good_features_to_track
│      │  │      GoodFeaturesToTrackDemo.java
│      │  │
│      │  └─harris_detector
│      │          CornerHarrisDemo.java
│      │
│      └─video
│          └─background_subtraction
│                  BackgroundSubtractionDemo.java
│
├─opencl
│      CMakeLists.txt
│      opencl-opencv-interop.cpp
│
├─opengl
│      CMakeLists.txt
│      opengl.cpp
│      opengl_interop.cpp
│      winapp.hpp
│
├─openvx
│      CMakeLists.txt
│      no_wrappers.cpp
│      wrappers.cpp
│      wrappers_video.cpp
│
├─python
│  │  asift.py
│  │  browse.py
│  │  calibrate.py
│  │  camera_calibration_show_extrinsics.py
│  │  camshift.py
│  │  CMakeLists.txt
│  │  coherence.py
│  │  color_histogram.py
│  │  common.py
│  │  contours.py
│  │  deconvolution.py
│  │  demo.py
│  │  dft.py
│  │  digits.py
│  │  digits_adjust.py
│  │  digits_video.py
│  │  distrans.py
│  │  dis_opt_flow.py
│  │  edge.py
│  │  facedetect.py
│  │  feature_homography.py
│  │  find_obj.py
│  │  fitline.py
│  │  floodfill.py
│  │  gabor_threads.py
│  │  gaussian_mix.py
│  │  grabcut.py
│  │  hist.py
│  │  houghcircles.py
│  │  houghlines.py
│  │  inpaint.py
│  │  kalman.py
│  │  kmeans.py
│  │  lappyr.py
│  │  letter_recog.py
│  │  lk_homography.py
│  │  lk_track.py
│  │  logpolar.py
│  │  morphology.py
│  │  mosse.py
│  │  mouse_and_match.py
│  │  mser.py
│  │  opencv_version.py
│  │  opt_flow.py
│  │  peopledetect.py
│  │  plane_ar.py
│  │  plane_tracker.py
│  │  squares.py
│  │  stereo_match.py
│  │  stitching.py
│  │  stitching_detailed.py
│  │  texture_flow.py
│  │  tst_scene_render.py
│  │  turing.py
│  │  video.py
│  │  video_threaded.py
│  │  video_v4l2.py
│  │  watershed.py
│  │  _coverage.py
│  │  _doc.py
│  │  _run_winpack_demo.cmd
│  │
│  └─tutorial_code
│      ├─core
│      │  ├─AddingImages
│      │  │      adding_images.py
│      │  │
│      │  ├─discrete_fourier_transform
│      │  │      discrete_fourier_transform.py
│      │  │
│      │  ├─mat_mask_operations
│      │  │      mat_mask_operations.py
│      │  │
│      │  └─mat_operations
│      │          mat_operations.py
│      │
│      ├─features2D
│      │  ├─akaze_matching
│      │  │      AKAZE_match.py
│      │  │
│      │  ├─feature_description
│      │  │      SURF_matching_Demo.py
│      │  │
│      │  ├─feature_detection
│      │  │      SURF_detection_Demo.py
│      │  │
│      │  ├─feature_flann_matcher
│      │  │      SURF_FLANN_matching_Demo.py
│      │  │
│      │  └─feature_homography
│      │          SURF_FLANN_matching_homography_Demo.py
│      │
│      ├─highgui
│      │  └─trackbar
│      │          AddingImagesTrackbar.py
│      │
│      ├─Histograms_Matching
│      │  ├─back_projection
│      │  │      calcBackProject_Demo1.py
│      │  │      calcBackProject_Demo2.py
│      │  │
│      │  ├─histogram_calculation
│      │  │      calcHist_Demo.py
│      │  │
│      │  ├─histogram_comparison
│      │  │      compareHist_Demo.py
│      │  │
│      │  └─histogram_equalization
│      │          EqualizeHist_Demo.py
│      │
│      ├─imgProc
│      │  ├─anisotropic_image_segmentation
│      │  │      anisotropic_image_segmentation.py
│      │  │
│      │  ├─BasicGeometricDrawing
│      │  │      basic_geometric_drawing.py
│      │  │
│      │  ├─changing_contrast_brightness_image
│      │  │      BasicLinearTransforms.py
│      │  │      changing_contrast_brightness_image.py
│      │  │
│      │  ├─erosion_dilatation
│      │  │      morphology_1.py
│      │  │
│      │  ├─HitMiss
│      │  │      hit_miss.py
│      │  │
│      │  ├─hough_line_transform
│      │  │      hough_line_transform.py
│      │  │      probabilistic_hough_line_transform.py
│      │  │
│      │  ├─match_template
│      │  │      match_template.py
│      │  │
│      │  ├─morph_lines_detection
│      │  │      morph_lines_detection.py
│      │  │
│      │  ├─opening_closing_hats
│      │  │      morphology_2.py
│      │  │
│      │  ├─Pyramids
│      │  │      pyramids.py
│      │  │
│      │  ├─Smoothing
│      │  │      smoothing.py
│      │  │
│      │  ├─threshold
│      │  │      threshold.py
│      │  │
│      │  └─threshold_inRange
│      │          threshold_inRange.py
│      │
│      ├─ImgTrans
│      │  ├─canny_detector
│      │  │      CannyDetector_Demo.py
│      │  │
│      │  ├─distance_transformation
│      │  │      imageSegmentation.py
│      │  │
│      │  ├─Filter2D
│      │  │      filter2D.py
│      │  │
│      │  ├─HoughCircle
│      │  │      hough_circle.py
│      │  │
│      │  ├─HoughLine
│      │  │      hough_lines.py
│      │  │
│      │  ├─LaPlace
│      │  │      laplace_demo.py
│      │  │
│      │  ├─MakeBorder
│      │  │      copy_make_border.py
│      │  │
│      │  ├─remap
│      │  │      Remap_Demo.py
│      │  │
│      │  ├─SobelDemo
│      │  │      sobel_demo.py
│      │  │
│      │  └─warp_affine
│      │          Geometric_Transforms_Demo.py
│      │
│      ├─introduction
│      │  └─documentation
│      │          documentation.py
│      │
│      ├─ml
│      │  ├─introduction_to_pca
│      │  │      introduction_to_pca.py
│      │  │
│      │  ├─introduction_to_svm
│      │  │      introduction_to_svm.py
│      │  │
│      │  ├─non_linear_svms
│      │  │      non_linear_svms.py
│      │  │
│      │  └─py_svm_opencv
│      │          hogsvm.py
│      │
│      ├─objectDetection
│      │  └─cascade_classifier
│      │          objectDetection.py
│      │
│      ├─photo
│      │  └─hdr_imaging
│      │          hdr_imaging.py
│      │
│      ├─ShapeDescriptors
│      │  ├─bounding_rects_circles
│      │  │      generalContours_demo1.py
│      │  │
│      │  ├─bounding_rotated_ellipses
│      │  │      generalContours_demo2.py
│      │  │
│      │  ├─find_contours
│      │  │      findContours_demo.py
│      │  │
│      │  ├─hull
│      │  │      hull_demo.py
│      │  │
│      │  ├─moments
│      │  │      moments_demo.py
│      │  │
│      │  └─point_polygon_test
│      │          pointPolygonTest_demo.py
│      │
│      ├─TrackingMotion
│      │  ├─corner_subpixels
│      │  │      cornerSubPix_Demo.py
│      │  │
│      │  ├─generic_corner_detector
│      │  │      cornerDetector_Demo.py
│      │  │
│      │  ├─good_features_to_track
│      │  │      goodFeaturesToTrack_Demo.py
│      │  │
│      │  └─harris_detector
│      │          cornerHarris_Demo.py
│      │
│      └─video
│          ├─background_subtraction
│          │      bg_sub.py
│          │
│          ├─meanshift
│          │      camshift.py
│          │      meanshift.py
│          │
│          └─optical_flow
│                  optical_flow.py
│                  optical_flow_dense.py
│
├─tapi
│      bgfg_segm.cpp
│      camshift.cpp
│      clahe.cpp
│      CMakeLists.txt
│      dense_optical_flow.cpp
│      hog.cpp
│      opencl_custom_kernel.cpp
│      pyrlk_optical_flow.cpp
│      squares.cpp
│      ufacedetect.cpp
│
├─va_intel
│      CMakeLists.txt
│      display.cpp.inc
│      va_intel_interop.cpp
│
├─winrt
│  │  readme.txt
│  │
│  ├─FaceDetection
│  │  │  FaceDetection.sln
│  │  │
│  │  └─FaceDetection
│  │      │  App.xaml
│  │      │  App.xaml.cpp
│  │      │  App.xaml.h
│  │      │  FaceDetection.vcxproj
│  │      │  FaceDetection.vcxproj.filters
│  │      │  FaceDetection_TemporaryKey.pfx
│  │      │  MainPage.xaml
│  │      │  MainPage.xaml.cpp
│  │      │  MainPage.xaml.h
│  │      │  opencv.props
│  │      │  Package.appxmanifest
│  │      │  pch.cpp
│  │      │  pch.h
│  │      │
│  │      └─Assets
│  │              group1.jpg
│  │              group2.jpg
│  │              group3.jpg
│  │              haarcascade_frontalface_alt.xml
│  │              Logo.scale-100.png
│  │              SmallLogo.scale-100.png
│  │              SplashScreen.scale-100.png
│  │              StoreLogo.scale-100.png
│  │
│  ├─ImageManipulations
│  │  │  AdvancedCapture.xaml
│  │  │  AdvancedCapture.xaml.cpp
│  │  │  AdvancedCapture.xaml.h
│  │  │  App.xaml
│  │  │  App.xaml.cpp
│  │  │  App.xaml.h
│  │  │  Constants.cpp
│  │  │  Constants.h
│  │  │  MainPage.xaml
│  │  │  MainPage.xaml.cpp
│  │  │  MainPage.xaml.h
│  │  │  MediaCapture.sln
│  │  │  MediaCapture.vcxproj
│  │  │  MediaCapture.vcxproj.filters
│  │  │  MediaCapture_TemporaryKey.pfx
│  │  │  opencv.props
│  │  │  Package.appxmanifest
│  │  │  pch.cpp
│  │  │  pch.h
│  │  │
│  │  ├─assets
│  │  │      opencv-logo-150.png
│  │  │      opencv-logo-30.png
│  │  │      splash-sdk.png
│  │  │      StoreLogo.png
│  │  │      windows-sdk.png
│  │  │      windows-sdk.scale-100.png
│  │  │
│  │  ├─common
│  │  │      LayoutAwarePage.cpp
│  │  │      LayoutAwarePage.h
│  │  │      StandardStyles.xaml
│  │  │      suspensionmanager.cpp
│  │  │      suspensionmanager.h
│  │  │
│  │  ├─MediaExtensions
│  │  │  ├─Common
│  │  │  │      AsyncCB.h
│  │  │  │      BufferLock.h
│  │  │  │      CritSec.h
│  │  │  │      LinkList.h
│  │  │  │      OpQueue.h
│  │  │  │
│  │  │  └─OcvTransform
│  │  │          dllmain.cpp
│  │  │          OcvImageManipulations.idl
│  │  │          OcvTransform.cpp
│  │  │          OcvTransform.def
│  │  │          OcvTransform.h
│  │  │          OcvTransform.vcxproj
│  │  │          opencv.props
│  │  │
│  │  └─sample-utils
│  │          SampleTemplateStyles.xaml
│  │
│  ├─JavaScript
│  │  │  default.html
│  │  │  MediaCaptureJavaScript.jsproj
│  │  │  MediaCaptureJavaScript.sln
│  │  │  MediaCaptureJavaScript_TemporaryKey.pfx
│  │  │  package.appxmanifest
│  │  │
│  │  ├─css
│  │  │      default.css
│  │  │
│  │  ├─html
│  │  │      AdvancedCapture.html
│  │  │
│  │  ├─images
│  │  │      logo.scale-100.png
│  │  │      microsoft-sdk.png
│  │  │      smalllogo.scale-100.png
│  │  │      smallTile-sdk.png
│  │  │      splash-sdk.png
│  │  │      splashscreen.scale-100.png
│  │  │      squareTile-sdk.png
│  │  │      storeLogo-sdk.png
│  │  │      storelogo.scale-100.png
│  │  │      tile-sdk.png
│  │  │      windows-sdk.png
│  │  │
│  │  ├─js
│  │  │      AdvancedCapture.js
│  │  │      default.js
│  │  │
│  │  └─sample-utils
│  │          sample-utils.css
│  │          sample-utils.js
│  │          scenario-select.html
│  │
│  └─OcvImageProcessing
│      │  OcvImageProcessing.sln
│      │
│      └─OcvImageProcessing
│          │  App.xaml
│          │  App.xaml.cpp
│          │  App.xaml.h
│          │  MainPage.xaml
│          │  MainPage.xaml.cpp
│          │  MainPage.xaml.h
│          │  OcvImageProcessing.vcxproj
│          │  OcvImageProcessing.vcxproj.filters
│          │  OcvImageProcessing_TemporaryKey.pfx
│          │  opencv.props
│          │  Package.appxmanifest
│          │  pch.cpp
│          │  pch.h
│          │
│          ├─Assets
│          │      Lena.png
│          │      Logo.png
│          │      SmallLogo.png
│          │      SplashScreen.png
│          │      StoreLogo.png
│          │
│          └─Common
│                  StandardStyles.xaml
│
├─winrt_universal
│  │  readme.txt
│  │
│  ├─PhoneTutorial
│  │  │  App.xaml
│  │  │  App.xaml.cpp
│  │  │  App.xaml.h
│  │  │  Lena.png
│  │  │  MainPage.xaml
│  │  │  MainPage.xaml.cpp
│  │  │  MainPage.xaml.h
│  │  │  opencv.props
│  │  │  Package.appxmanifest
│  │  │  pch.cpp
│  │  │  pch.h
│  │  │  PhoneTutorial.sln
│  │  │  PhoneTutorial.vcxproj
│  │  │  PhoneTutorial.vcxproj.filters
│  │  │
│  │  └─Assets
│  │          Logo.scale-240.png
│  │          SmallLogo.scale-240.png
│  │          SplashScreen.scale-240.png
│  │          Square71x71Logo.scale-240.png
│  │          StoreLogo.scale-240.png
│  │          WideLogo.scale-240.png
│  │
│  └─VideoCaptureXAML
│      │  VideoCaptureXAML.sln
│      │
│      └─video_capture_xaml
│          │  opencv.props
│          │
│          ├─video_capture_xaml.Shared
│          │      App.xaml
│          │      App.xaml.cpp
│          │      App.xaml.h
│          │      main.cpp
│          │      pch.cpp
│          │      pch.h
│          │      video_capture_xaml.Shared.vcxitems
│          │      video_capture_xaml.Shared.vcxitems.filters
│          │
│          ├─video_capture_xaml.Windows
│          │  │  MainPage.xaml
│          │  │  MainPage.xaml.cpp
│          │  │  MainPage.xaml.h
│          │  │  Package.appxmanifest
│          │  │  readme.txt
│          │  │  video_capture_xaml.Windows.vcxproj
│          │  │  video_capture_xaml.Windows.vcxproj.filters
│          │  │  video_capture_xaml.Windows_TemporaryKey.pfx
│          │  │
│          │  └─Assets
│          │          haarcascade_frontalface_alt.xml
│          │          Logo.scale-100.png
│          │          SmallLogo.scale-100.png
│          │          SplashScreen.scale-100.png
│          │          StoreLogo.scale-100.png
│          │
│          └─video_capture_xaml.WindowsPhone
│              │  MainPage.xaml
│              │  MainPage.xaml.cpp
│              │  MainPage.xaml.h
│              │  Package.appxmanifest
│              │  video_capture_xaml.WindowsPhone.vcxproj
│              │  video_capture_xaml.WindowsPhone.vcxproj.filters
│              │  video_capture_xaml.WindowsPhone_TemporaryKey.pfx
│              │
│              └─Assets
│                      Logo.scale-240.png
│                      SmallLogo.scale-240.png
│                      SplashScreen.scale-240.png
│                      Square71x71Logo.scale-240.png
│                      StoreLogo.scale-240.png
│                      WideLogo.scale-240.png
│
└─wp8
    │  readme.txt
    │
    ├─OcvImageManipulation
    │  │  ImageManipulation.sln
    │  │
    │  └─PhoneXamlDirect3DApp1
    │      ├─PhoneXamlDirect3DApp1
    │      │  │  App.xaml
    │      │  │  App.xaml.cs
    │      │  │  LocalizedStrings.cs
    │      │  │  MainPage.xaml
    │      │  │  MainPage.xaml.cs
    │      │  │  OcvImageManipulation.csproj
    │      │  │  SplashScreenImage.jpg
    │      │  │
    │      │  ├─Assets
    │      │  │  │  AlignmentGrid.png
    │      │  │  │  ApplicationIcon.png
    │      │  │  │
    │      │  │  └─Tiles
    │      │  │          FlipCycleTileLarge.png
    │      │  │          FlipCycleTileMedium.png
    │      │  │          FlipCycleTileSmall.png
    │      │  │          IconicTileMediumLarge.png
    │      │  │          IconicTileSmall.png
    │      │  │
    │      │  ├─Properties
    │      │  │      AppManifest.xml
    │      │  │      AssemblyInfo.cs
    │      │  │      WMAppManifest.xml
    │      │  │
    │      │  └─Resources
    │      │          AppResources.Designer.cs
    │      │          AppResources.resx
    │      │
    │      └─PhoneXamlDirect3DApp1Comp
    │              BasicTimer.h
    │              Direct3DBase.cpp
    │              Direct3DBase.h
    │              Direct3DContentProvider.cpp
    │              Direct3DContentProvider.h
    │              Direct3DInterop.cpp
    │              Direct3DInterop.h
    │              DirectXHelper.h
    │              opencv.props
    │              pch.cpp
    │              pch.h
    │              PhoneXamlDirect3DApp1Comp.vcxproj
    │              QuadRenderer.cpp
    │              QuadRenderer.h
    │              SimplePixelShader.hlsl
    │              SimpleVertexShader.hlsl
    │
    ├─OcvRotatingCube
    │  │  OcvRotatingCube.sln
    │  │
    │  └─PhoneXamlDirect3DApp1
    │      ├─PhoneXamlDirect3DApp1
    │      │  │  App.xaml
    │      │  │  App.xaml.cs
    │      │  │  LocalizedStrings.cs
    │      │  │  MainPage.xaml
    │      │  │  MainPage.xaml.cs
    │      │  │  OcvRotatingCube.csproj
    │      │  │  SplashScreenImage.jpg
    │      │  │
    │      │  ├─Assets
    │      │  │  │  AlignmentGrid.png
    │      │  │  │  ApplicationIcon.png
    │      │  │  │  Lena.png
    │      │  │  │
    │      │  │  └─Tiles
    │      │  │          FlipCycleTileLarge.png
    │      │  │          FlipCycleTileMedium.png
    │      │  │          FlipCycleTileSmall.png
    │      │  │          IconicTileMediumLarge.png
    │      │  │          IconicTileSmall.png
    │      │  │
    │      │  ├─Properties
    │      │  │      AppManifest.xml
    │      │  │      AssemblyInfo.cs
    │      │  │      WMAppManifest.xml
    │      │  │
    │      │  └─Resources
    │      │          AppResources.Designer.cs
    │      │          AppResources.resx
    │      │
    │      └─PhoneXamlDirect3DApp1Comp
    │              BasicTimer.h
    │              CubeRenderer.cpp
    │              CubeRenderer.h
    │              Direct3DBase.cpp
    │              Direct3DBase.h
    │              Direct3DContentProvider.cpp
    │              Direct3DContentProvider.h
    │              Direct3DInterop.cpp
    │              Direct3DInterop.h
    │              DirectXHelper.h
    │              opencv.props
    │              pch.cpp
    │              pch.h
    │              PhoneXamlDirect3DApp1Comp.vcxproj
    │              SimplePixelShader.hlsl
    │              SimpleVertexShader.hlsl
    │
    └─OpenCVXaml
        │  OpenCVXaml.sln
        │
        ├─OpenCVComponent
        │      opencv.props
        │      OpenCVComponent.cpp
        │      OpenCVComponent.h
        │      OpenCVComponent.vcxproj
        │      OpenCVComponent.vcxproj.filters
        │      pch.cpp
        │      pch.h
        │
        └─OpenCVXaml
            │  App.xaml
            │  App.xaml.cs
            │  LocalizedStrings.cs
            │  MainPage.xaml
            │  MainPage.xaml.cs
            │  OpenCVXaml.csproj
            │
            ├─Assets
            │  │  AlignmentGrid.png
            │  │  ApplicationIcon.png
            │  │  Lena.png
            │  │
            │  └─Tiles
            │          FlipCycleTileLarge.png
            │          FlipCycleTileMedium.png
            │          FlipCycleTileSmall.png
            │          IconicTileMediumLarge.png
            │          IconicTileSmall.png
            │
            ├─Properties
            │      AppManifest.xml
            │      AssemblyInfo.cs
            │      WMAppManifest.xml
            │
            └─Resources
                    AppResources.Designer.cs
                    AppResources.resx
```

## 尾巴

生成目录后发现，里面的例子数量多出了我的意料，这么看来是一条漫漫学习路了。

---

本文由芒果浩明发布，转载需注明来源。
本文链接：https://mangoroom.cn/opencv/opencv-sample3-files-tree.html