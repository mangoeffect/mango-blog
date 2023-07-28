---
title: "基于opencv-simpleblobdetector封装blob检测算子"
categories: [ "opencv","算法","c++","计算机视觉" ]
tags: [ "opencv","blob","contours","area" ]
draft: false
slug: "better-blob-detection-based-on-simepleblobdetector"
date: "2020-04-01 22:11:00"
---

## 简介

文本首先介绍了opencv库内置SimpleBlobDetector检测算子的简单用法，探讨其检测输出结果，分析其局限性。而后针对去现在机器视觉普遍的blob检测需求对SimpleBlobDetector检测算子进行拓展，设计并封装新的检测算子,芒果将其命名为BlobDetect。BlobDetect算子在SimpleBlobDetector算子的基础上拓展了其检测的输出结果，支持输出blob列表、每个blob的面积、半径、位置、检测得分以及轮廓等。

## SimpleBlobDetector

首先来瞧瞧opencv自带的SimpleBlobDetector算子，其使用方法和输出得检测结果非常地简单，简单到使用一遍后就可以体会opencv为什么将其命名为SimpleBlobDetector，你会发现这个检测的blob结果信息可真够simple的。有多么simple呢，接着往下看。

先上一段代码：

```c++
Ptr<cv::SimpleBlobDetector> sblobdetect = cv::SimpleBlobDetector::create();
std::vector<cv::KeyPoint> kps;
sblobdetect->detect(img, kps);
std::cout<<"keypoints size: "<<kps.size()<<std::endl;
int i = 0;
for(const auto& kp: kps)
{
    ++i;
    std::cout<<i<<"location: "<<kp.pt<<"diameter: "<<kp.size<<std::endl;
}
sblobdetect.release();
```
以上代码便是一个简单的SimpleBlobDetector算子使用流程，再深入一些也只有检测之前参数的设置以及检测后结果的渲染绘制。渲染检测的结果非常简单，只需调用一个函数就可以了

```c++
cv::Mat im_with_keypoints;
sblobdetect->detect(img, kps);
cv::drawKeypoints( img, kps, im_with_keypoints, Scalar(0,0,255), DrawMatchesFlags::DRAW_RICH_KEYPOINTS );
cv::imshow("keypoints", im_with_keypoints );
```

检测前参数的设置也不复杂，在

```c++
sblobdetect->detect(img, kps);
```
之前设置一些参数来限制blob的检测搜索，如以blob的面积为条件设置参数

```c++

// Setup SimpleBlobDetector parameters.
SimpleBlobDetector::Params params;

// Filter by Area.
params.filterByArea = true;
params.minArea = 1500;
sblobdetect->detect(img, kps);
```
这样就完成了限定只获取面积大于1500像素的blob设置，其他参数设置也可以同样地设置，这里不再赘述。咱们重点关注这个检测结果

```c++
std::vector<cv::KeyPoint> kps;
sblobdetect->detect(img, kps);
std::cout<<"keypoints size: "<<kps.size()<<std::endl;
int i = 0;
for(const auto& kp: kps)
{
    ++i;
    std::cout<<i<<"location: "<<kp.pt<<"diameter: "<<kp.size<<std::endl;
}
```
在使用SimpleBlobDetector检测blob后得到一个KeyPoint类型的keypoints，从上面代码可以看到我只获取到了location（位置）和diameter（直径）两个信息，而关于blob的其他信息如面积就一概不知了。看到这里你可能不止明白为什么这货只名字前加个simple了，simple到让人觉得这输出结果简直不能接受，这么点信息简直就是半残的算子。

## 扩展SimpleBlobDetector

### 扩展内容

鉴于SimpleBlobDetector的结果过于残缺，我决定对SimpleBlobDetector进行修改和扩展。鉴于SimpleBlobDetector参数设置和检测流程已经够用了，方法也很简单，所以拓展和修改就从输出的结果出发，来封装自己的blob检测算子。在参考了一些商业的机器视觉算法平台和算法库之后，发现普遍的blob检测算子输出结果会包含以下几个内容：

- location: blob的坐标位置
- area：blob的面积
- radius： blob的半径
- outline: blob的轮廓
- confidence：blob的检测分数

### 如何求取扩展的内容

确定了需要得到的blob检测信息后，接下来需要思考的就是在SimpleBlobDetector的基础上如何获取这些信息。咱逐一分析

- location：SimpleBlobDetector已经得出，直接继承即可
- radius: blob半径，SimpleBlobDetector已经得出diameter，取其一半即为半径radius.
- area&outline: blob面积和轮廓点，方法1：利用location和radius信息，在blob所在区域使用findContours函数查找和距计算可以得到blob的轮廓和面积。方法2：思路与方法1类似，但比方法1更近一步是直接重写detect函数，在计算location与radius的同时将area和outline计算出来，这样比方法1效率要高的多。
- confindence: blob的检测分数，此分数在SimpleBlobDetector内部进行检测计算的时候得出，无法从blob的结果逆推计算confidence，只能在检测计算的时候一半保存输出。

所以在综合考虑后，觉得重写detect函数，所有的结算结果都由新的detect函数给出。基于以上的分析，下面开始设计新的blob检测算子

### BlobDetect设计

（1） 单个blob信息

```c++
 // Single blob info
struct BlobInfo
{
    cv::Point2d location;                        //center point's coordinate
    std::vector<cv::Point2d> outline;            //outline
    double area;                                 //area
    double radius;                               //radius
    double confidence;                           //blob detection confidence
};//BlobInfo
```
（2）blob检测结果

```c++
struct BlobDetectResult
{
    std::vector<BlobInfo> blobList;
};//BlobDetectResult
```
（3）新检测算子BlobDetect

```c++
class BlobDetect: protected SimpleBlobDetector
{
public:
    explicit BlobDetect(const SimpleBlobDetector::Params &parameters = SimpleBlobDetector::Params());

    static Ptr<BlobDetect> CreateInstance(const SimpleBlobDetector::Params &parameters = SimpleBlobDetector::Params());


    // Workflow
    // 1.
    void Init(cv::Mat& inputImage);                                // input image
    // 2.
    void SetParams();                                              // default value
    void SetParams(std::string name, float value);                 // set value by param name
    // 3. detection
    // ....

    void Run();                                                    // run detection processing

    // --------------debug---------------------------
    void PrintResultInfo() const;                                        // print result information of detection
    void PrintParameter() const;



    // class members for user
    cv::Mat inputImage;
    BlobDetectResult result;
protected:

private:
    void FindBlobs(InputArray image, InputArray binaryImage, std::vector<BlobInfo> &centers) const;
    void Detect( InputArray image, InputArray mask=noArray() );

    std::vector<cv::KeyPoint> keyPoints;
    Params params;
};//BlobDetect
```

以上的BlobDetect类设计继承了SimpleBlobDetector类，并且将原来detect函数重写了放在private隐藏，改为暴露三个工作流程函数：

```c++
// Workflow
// 1.
void Init(cv::Mat& inputImage);                                // input image
// 2.
void SetParams();                                              // default value
void SetParams(std::string name, float value);                 // set value by param name
// 3. detection
// ....

void Run();   
```
而原来结果数据```std::vector<cv::KeyPoint> keyPoints```也隐藏起来，只暴露的两个数据是结果结构体以及输入图像。所以，新的检测类使用方式是这样的：

```c++
//1. 创建算子
Ptr<mv::BlobDetect> blobdetect = mv::BlobDetect::CreateInstance();
//2. 初始化输入
blobdetect->Init(img);
//3. 设置参数（可选）
blobdetect->SetParams();
//4. 执行算子
blobdetect->Run();
//5. 获取结果数据
//blobdetect->result
//6. 结束释放
blobdetect.release();
```
这么一来会相对清晰一些，接下来代码中是如何获取这些结果信息的，在以下两个函数中可以找到答案

```c++
void FindBlobs(InputArray image, InputArray binaryImage, std::vector<BlobInfo> &centers) const;
void Detect( InputArray image, InputArray mask=noArray() );
```

先是FindBlobs

```c++
Ptr<mv::BlobDetect> mv::BlobDetect::CreateInstance(const SimpleBlobDetector::Params &parameters)
{
    return cv::makePtr<mv::BlobDetect>(parameters);
}//CreateInstance

void mv::BlobDetect::Detect(const _InputArray &image, const _InputArray &mask)
{
    keyPoints.clear();
    CV_Assert(params.minRepeatability != 0);
    Mat grayscaleImage;
    if (image.channels() == 3 || image.channels() == 4)
        cvtColor(image, grayscaleImage, COLOR_BGR2GRAY);
    else
        grayscaleImage = image.getMat();

    if (grayscaleImage.type() != CV_8UC1) {
        CV_Error(Error::StsUnsupportedFormat, "Blob detector only supports 8-bit images!");
    }

    std::vector < std::vector<BlobInfo> > centers;

    for (double thresh = params.minThreshold; thresh < params.maxThreshold; thresh += params.thresholdStep)
    {
        Mat binarizedImage;
        threshold(grayscaleImage, binarizedImage, thresh, 255, THRESH_BINARY);

        std::vector < BlobInfo > curCenters;
        FindBlobs(grayscaleImage, binarizedImage, curCenters);
        std::vector < std::vector<BlobInfo> > newCenters;
        for (size_t i = 0; i < curCenters.size(); i++)
        {
            bool isNew = true;
            for (size_t j = 0; j < centers.size(); j++)
            {
                double dist = norm(centers[j][ centers[j].size() / 2 ].location - curCenters[i].location);
                isNew = dist >= params.minDistBetweenBlobs && dist >= centers[j][ centers[j].size() / 2 ].radius && dist >= curCenters[i].radius;
                if (!isNew)
                {
                    centers[j].push_back(curCenters[i]);

                    size_t k = centers[j].size() - 1;
                    while( k > 0 && curCenters[i].radius < centers[j][k-1].radius )
                    {
                        centers[j][k] = centers[j][k-1];
                        k--;
                    }
                    centers[j][k] = curCenters[i];

                    break;
                }
            }
            if (isNew)
                newCenters.push_back(std::vector<BlobInfo> (1, curCenters[i]));
        }
        std::copy(newCenters.begin(), newCenters.end(), std::back_inserter(centers));
    }

    // parse centers to result
    result.blobList.clear();
    for (size_t i = 0; i < centers.size(); i++)
    {
        if (centers[i].size() < params.minRepeatability)
            continue;
        Point2d sumPoint(0, 0);
        double normalizer = 0;
        double sumArea = 0;
        for (size_t j = 0; j < centers[i].size(); j++)
        {
            sumPoint += centers[i][j].confidence * centers[i][j].location;
            normalizer += centers[i][j].confidence;
            sumArea += centers[i][j].area;
        }
        sumPoint *= (1. / normalizer);
        sumArea *= (1./centers[i].size());
        normalizer *= (1./centers[i].size());
        KeyPoint kpt(sumPoint, (float)(centers[i][centers[i].size() / 2].radius) * 2.0f);
        // parse centers to result
        BlobInfo bi = centers[i][centers[i].size() / 2];
        bi.location = sumPoint;
        bi.area = sumArea;
        bi.confidence = normalizer;
        result.blobList.push_back(bi);
        keyPoints.push_back(kpt);
    }

    if (!mask.empty())
    {
        KeyPointsFilter::runByPixelsMask(keyPoints, mask.getMat());
    }
}//Detect

void mv::BlobDetect::FindBlobs(const _InputArray &_image, const _InputArray &_binaryImage,
                               std::vector<BlobInfo> &centers) const
{
    Mat image = _image.getMat(), binaryImage = _binaryImage.getMat();
    CV_UNUSED(image);
    centers.clear();

    std::vector < std::vector<Point> > contours;
    findContours(binaryImage, contours, RETR_LIST, CHAIN_APPROX_NONE);

#ifdef DEBUG_BLOB_DETECTOR
    Mat keypointsImage;
cvtColor(binaryImage, keypointsImage, COLOR_GRAY2RGB);

Mat contoursImage;
cvtColor(binaryImage, contoursImage, COLOR_GRAY2RGB);
drawContours( contoursImage, contours, -1, Scalar(0,255,0) );
imshow("contours", contoursImage );
#endif

    for (size_t contourIdx = 0; contourIdx < contours.size(); contourIdx++)
    {
        BlobInfo center;
        center.confidence = 1;


        Moments moms = moments(contours[contourIdx]);
        if (params.filterByArea)
        {
            double area = moms.m00;
            if (area < params.minArea || area >= params.maxArea)
                continue;
        }

        if (params.filterByCircularity)
        {
            double area = moms.m00;
            double perimeter = arcLength(contours[contourIdx], true);
            double ratio = 4 * CV_PI * area / (perimeter * perimeter);
            if (ratio < params.minCircularity || ratio >= params.maxCircularity)
                continue;
        }

        if (params.filterByInertia)
        {
            double denominator = std::sqrt(std::pow(2 * moms.mu11, 2) + std::pow(moms.mu20 - moms.mu02, 2));
            const double eps = 1e-2;
            double ratio;
            if (denominator > eps)
            {
                double cosmin = (moms.mu20 - moms.mu02) / denominator;
                double sinmin = 2 * moms.mu11 / denominator;
                double cosmax = -cosmin;
                double sinmax = -sinmin;

                double imin = 0.5 * (moms.mu20 + moms.mu02) - 0.5 * (moms.mu20 - moms.mu02) * cosmin - moms.mu11 * sinmin;
                double imax = 0.5 * (moms.mu20 + moms.mu02) - 0.5 * (moms.mu20 - moms.mu02) * cosmax - moms.mu11 * sinmax;
                ratio = imin / imax;
            }
            else
            {
                ratio = 1;
            }

            if (ratio < params.minInertiaRatio || ratio >= params.maxInertiaRatio)
                continue;

            center.confidence = ratio * ratio;
        }

        if (params.filterByConvexity)
        {
            std::vector < Point > hull;
            convexHull(contours[contourIdx], hull);
            double area = contourArea(contours[contourIdx]);
            double hullArea = contourArea(hull);
            if (fabs(hullArea) < DBL_EPSILON)
                continue;
            double ratio = area / hullArea;
            if (ratio < params.minConvexity || ratio >= params.maxConvexity)
                continue;
        }

        if(moms.m00 == 0.0)
            continue;
        center.location = Point2d(moms.m10 / moms.m00, moms.m01 / moms.m00);

        if (params.filterByColor)
        {
            if (binaryImage.at<uchar> (cvRound(center.location.y), cvRound(center.location.x)) != params.blobColor)
                continue;
        }
        // area
        center.area = moms.m00;
        //compute blob radius
        {
            std::vector<double> dists;
            for (size_t pointIdx = 0; pointIdx < contours[contourIdx].size(); pointIdx++)
            {
                Point2d pt = contours[contourIdx][pointIdx];
                dists.push_back(norm(center.location - pt));
            }
            std::sort(dists.begin(), dists.end());
            center.radius = (dists[(dists.size() - 1) / 2] + dists[dists.size() / 2]) / 2.;
        }
        //get blob outline
        center.outline.clear();
        // save blob outline
        center.outline.assign(contours[contourIdx].begin(), contours[contourIdx].end());
        centers.push_back(center);
#ifdef DEBUG_BLOB_DETECTOR
        circle( keypointsImage, center.location, 1, Scalar(0,0,255), 1 );
#endif
    }
#ifdef DEBUG_BLOB_DETECTOR
    imshow("bk", keypointsImage );
waitKey();
#endif
}//FindBlobs
```

接着重写的Detect函数

```c++
void mv::BlobDetect::Detect(const _InputArray &image, const _InputArray &mask)
{
    keyPoints.clear();
    CV_Assert(params.minRepeatability != 0);
    Mat grayscaleImage;
    if (image.channels() == 3 || image.channels() == 4)
        cvtColor(image, grayscaleImage, COLOR_BGR2GRAY);
    else
        grayscaleImage = image.getMat();

    if (grayscaleImage.type() != CV_8UC1) {
        CV_Error(Error::StsUnsupportedFormat, "Blob detector only supports 8-bit images!");
    }

    std::vector < std::vector<BlobInfo> > centers;

    for (double thresh = params.minThreshold; thresh < params.maxThreshold; thresh += params.thresholdStep)
    {
        Mat binarizedImage;
        threshold(grayscaleImage, binarizedImage, thresh, 255, THRESH_BINARY);

        std::vector < BlobInfo > curCenters;
        FindBlobs(grayscaleImage, binarizedImage, curCenters);
        std::vector < std::vector<BlobInfo> > newCenters;
        for (size_t i = 0; i < curCenters.size(); i++)
        {
            bool isNew = true;
            for (size_t j = 0; j < centers.size(); j++)
            {
                double dist = norm(centers[j][ centers[j].size() / 2 ].location - curCenters[i].location);
                isNew = dist >= params.minDistBetweenBlobs && dist >= centers[j][ centers[j].size() / 2 ].radius && dist >= curCenters[i].radius;
                if (!isNew)
                {
                    centers[j].push_back(curCenters[i]);

                    size_t k = centers[j].size() - 1;
                    while( k > 0 && curCenters[i].radius < centers[j][k-1].radius )
                    {
                        centers[j][k] = centers[j][k-1];
                        k--;
                    }
                    centers[j][k] = curCenters[i];

                    break;
                }
            }
            if (isNew)
                newCenters.push_back(std::vector<BlobInfo> (1, curCenters[i]));
        }
        std::copy(newCenters.begin(), newCenters.end(), std::back_inserter(centers));
    }

    // parse centers to result
    result.blobList.clear();
    for (size_t i = 0; i < centers.size(); i++)
    {
        if (centers[i].size() < params.minRepeatability)
            continue;
        Point2d sumPoint(0, 0);
        double normalizer = 0;
        double sumArea = 0;
        for (size_t j = 0; j < centers[i].size(); j++)
        {
            sumPoint += centers[i][j].confidence * centers[i][j].location;
            normalizer += centers[i][j].confidence;
            sumArea += centers[i][j].area;
        }
        sumPoint *= (1. / normalizer);
        sumArea *= (1./centers[i].size());
        normalizer *= (1./centers[i].size());
        KeyPoint kpt(sumPoint, (float)(centers[i][centers[i].size() / 2].radius) * 2.0f);
        // parse centers to result
        BlobInfo bi = centers[i][centers[i].size() / 2];
        bi.location = sumPoint;
        bi.area = sumArea;
        bi.confidence = normalizer;
        result.blobList.push_back(bi);
        keyPoints.push_back(kpt);
    }

    if (!mask.empty())
    {
        KeyPointsFilter::runByPixelsMask(keyPoints, mask.getMat());
    }
}//Detect
```
其实工作量并不大，如果你看过opencv源码，就会发现芒果其实是将其源码加以修改，这两个函数在源码中都存在，我只是在其基础上替换了keypoint的计算过程，在其计算的过程将一些本该保存下来的结果给保存下来了。所以封装的BlobDetect算子在理论上算法复杂度与SimpleBlobDetector算子复杂度几乎没什么差别，检测的效果也一致，下面的测试结果也印着了这一点。

---

测试方式：读取一张图，BlobDetect算子与SimpleBlobDetector算子均采用相同的默认参数

![blob.jpg][1]
```c++
//
// Created by mango on 3/20/2020.
//

#include "blobdetect.h"
#include "opencv2/opencv.hpp"

#include <iostream>

int main()
{
    std::cout<<"blob detection"<<std::endl;
    Ptr<mv::BlobDetect> blobdetect = mv::BlobDetect::CreateInstance();
    cv::Mat img = cv::imread("F:\\Code\\machine-vision-algorithms-library\\test\\image\\blob.jpg");
    if(img.empty())
        return  -1;
    blobdetect->Init(img);
    blobdetect->SetParams();
    int s = cv::getTickCount();
    blobdetect->Run();
    int e = cv::getTickCount();
    std::cout<<"BlobDetect cost time: "<< static_cast<double >(e -  s) /cv::getTickFrequency()<<"ms" <<std::endl;
    blobdetect->PrintResultInfo();

//    blobdetect->PrintParameter();
//    blobdetect->DrawOutline();
    blobdetect.release();

    Ptr<cv::SimpleBlobDetector> sblobdetect = cv::SimpleBlobDetector::create();
    std::vector<cv::KeyPoint> kps;
    s = cv::getTickCount();
    sblobdetect->detect(img, kps);
    e = cv::getTickCount();
    std::cout<<"SimpleBlobDetector cost time: "<< static_cast<double >(e -  s) /cv::getTickFrequency()<<"ms" <<std::endl;
    std::cout<<"keypoints size: "<<kps.size()<<std::endl;
    for(int i = 0; i < kps.size(); ++i)
    {
        std::cout<<i+1<<": location:"<<kps[i].pt<<std::endl;
    }
    cv::Mat im_with_keypoints;
    cv::drawKeypoints( img, kps, im_with_keypoints, Scalar(0,0,255), DrawMatchesFlags::DRAW_RICH_KEYPOINTS );
    cv::imshow("keypoints", im_with_keypoints );
    cv::waitKey(0);
    sblobdetect.release();

    return 0;
}
```

测试结果：

```
BlobDetect cost time: 0.0117662ms
Blob num: 16
1: location:(382.92,393.728) radius: 32.527 area: 3331.59
2: location:(377.274,304.081) radius: 32.5371 area: 3330.79
3: location:(291.028,303.908) radius: 31.307 area: 3060.56
4: location:(198.933,304.062) radius: 27.4089 area: 2291.68
5: location:(284.935,229.03) radius: 29.1834 area: 2715.44
6: location:(197.001,229.024) radius: 30.7306 area: 3022.44
7: location:(103.599,235.077) radius: 25.6136 area: 1977.79
8: location:(377.275,229.082) radius: 32.5371 area: 3331.79
9: location:(377.279,145.087) radius: 32.5427 area: 3330.38
10: location:(103.498,75.1426) radius: 17.5397 area: 972
11: location:(189.339,75.0477) radius: 21.6148 area: 1471.68
12: location:(284.875,75.125) radius: 27.2657 area: 2341.76
13: location:(377.279,69.4519) radius: 32.7479 area: 3374.53
14: location:(284.956,145.039) radius: 32.2447 area: 3272.05
15: location:(290.906,394.351) radius: 32.5015 area: 3253.94
16: location:(196.959,145.037) radius: 32.2083 area: 3254
SimpleBlobDetector cost time: 0.011054ms
keypoints size: 16
1: location:[382.92, 393.728]
2: location:[377.274, 304.081]
3: location:[291.028, 303.908]
4: location:[198.933, 304.062]
5: location:[284.935, 229.03]
6: location:[197.001, 229.024]
7: location:[103.599, 235.077]
8: location:[377.275, 229.082]
9: location:[377.279, 145.087]
10: location:[103.498, 75.1426]
11: location:[189.339, 75.0477]
12: location:[284.875, 75.125]
13: location:[377.279, 69.452]
14: location:[284.956, 145.039]
15: location:[290.906, 394.351]
16: location:[196.959, 145.037]
```
![draw-keypoints.png][2]
![outline.png][3]

可以看到，检测输出blob的位置信息一致，BlobDetect多输出需要的信息，但是两者耗时相差无几（小于0.001ms）。完整源码放在github，如果觉得对你有所帮助，不妨抬手给个star。
[https://github.com/mangosroom/mv-kit/tree/master/src/remove/blobdetect](https://github.com/mangosroom/mv-kit/tree/master/src/remove/blobdetect)

##  reference

[【1】Blob Detection Using OpenCV ( Python, C++ )-www.learnopencv.com](https://www.learnopencv.com/blob-detection-using-opencv-python-c/)

-----

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/opencv/better-blob-detection-based-on-simepleblobdetector.html

  [1]: https://mangoroom.cn/usr/uploads/2020/04/1884716798.jpg
  [2]: https://mangoroom.cn/usr/uploads/2020/04/75016653.png
  [3]: https://mangoroom.cn/usr/uploads/2020/04/441672238.png