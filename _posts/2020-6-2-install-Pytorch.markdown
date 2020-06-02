---
layout:     post
title:      "Mac用Conda安装Pytorch"
subtitle:   "Anaconda换源"
date:       2020-06-02
author:     "SirJamie"
header-img: "img/head-post/post-bg-install-pytroch.png"
tags:
    - Pytorch
---

## 安装anaconda

anaconda镜像可以到[这里](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)下载

---

## anaconda 更换清华源

按照[官方文档](https://mirror.tuna.tsinghua.edu.cn/help/anaconda/)来就好

终端中运行 ``open ~/.condarc``

替换为
```
channels:
  - defaults
show_channel_urls: true
channel_alias: https://mirrors.tuna.tsinghua.edu.cn/anaconda
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud

```

运行 ``conda clean -i`` 清除索引缓存

运行 ``conda create -n myenv numpy`` 测试换源后的速度

---

## 安装Pytorch
按照[官网](https://pytorch.org/get-started/locally/)来就好 

运行 ``conda install pytorch torchvision -c pytorch`` 即可

---