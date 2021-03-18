---
layout:     post
title:      "Mac安装Opencv4，并使用Qt5调用"
subtitle:   "解决ld symbol(s) not found for architecture x86_64问题"
date:       2020-9-20
author:     "SirJamie"
header-img: "img/head-post/post-bg-jt.jpg"
tags:
    - Mac
    - OpenCV
---

## 安装[homebrew](https://brew.sh)

直接拷贝
``/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"``
到终端
回车运行即可

---

## homebrew安装OpenCV4

``brew install opencv``

Mac就是这么香（笑

---

## Qt调用

**重点来了嗷!**

新建完项目后，在项目的``.pro``文件中，添加头文件路径和动态链接库路径
```
INCLUDEPATH +=  /usr/local/include \
                /usr/local/include/opencv4 \
                /usr/local/include/opencv4/opencv2


LIBS += /usr/local/lib/libopencv_*
```
网上充斥着大量的错误写法,误人子弟！
```
#无法运行的写法
LIBS += /usr/local/lib
        -lopencv_core \
        -lopencv_highgui \
        -lopencv_imgproc \
```
这种写法是无法运行的，会报``ld symbol(s) not found for architecture x86_64``的错！

---