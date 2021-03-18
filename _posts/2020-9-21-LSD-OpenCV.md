---
layout:     post
title:      "爷青结！OpenCV4竟然没LSD了"
subtitle:   "使用FLD替代吧"
date:       2020-9-21
author:     "SirJamie"
header-img: "img/head-post/post-bg-lsd-curry.jpg"
tags:
    - OpenCV
---

## 使用FLD替代原来的LSD

对比看一下
```cpp
//LSD
cv::Ptr<cv::LineSegmentDetector> lsd = createLineSegmentDetector(cv::LSD_REFINE_STD);
lsd->detect(img,lines);
lsd->drawSegments(res,lines);
```

先包含一下头文件``#include "opencv2/ximgproc.hpp"``
```cpp
//FLD  a inplacement of LSD in OpenCV4 due to license issues
cv::Ptr<cv::ximgproc::FastLineDetector> fld= cv::ximgproc::createFastLineDetector();
fld->detect(img,lines);
fld->drawSegments(res,lines);
```

当然了，FLD基于Canny算子，对于小目标的效果肯定没有LSD好。

算惹，不折腾了，能~~冲~~用就行，哈哈哈哈哈

---
