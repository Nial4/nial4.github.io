---
layout: post
title: 'OpenCV Play AVIvideo'
tags: [CV, promgramming]
comments: true
---

## 第一个 OpenCV 程序。

OpenCV 已经更新了好几代了，我买的这本书还在用 OpenCV1.0，也就当入门娱乐了,关于 include lib 这些配置就不多说了，我把它放在 VC14 目录下就不用每次设置了

```cpp
#include "stdafx.h"
#include <opencv2/highgui/highgui.hpp>

using namespace cv;
int main(int argc, char** argv) {
  CvCapture* capture = cvCreateFileCapture("SLAM_8.avi");    //是一个读取函数，包括了AVI status information
  IplImage* frame;                       //这个类型是OpenCV的一个数据结构，single-channel，multichannel etc
  while (1) {
    frame = cvQueryFrame(capture);      //将下一帧载入内存
    if (!frame) break;
    cvShowImage("Example2", frame);
    char c = cvWaitKey(33);        //这里我们手动延迟www帧数，后面会有正确的做法的
    if (c == 27) break;            //27==Esc（ASCCII27)
  }
  cvReleaseCapture(&capture);     //释放内存
  cvDestroyWindow("Example3");
}
```
