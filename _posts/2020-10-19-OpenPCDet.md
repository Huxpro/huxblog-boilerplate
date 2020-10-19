---
layout:     post
title:      "安装spconv踩坑实录"
subtitle:   "配好环境就等于成功了一大半"
date:       2020-10-19
author:     "SirJamie"
header-img: "img/head-post/post-bg-OpenPCDet.jpeg"
tags:
    - linux
---

> 本人的版本 Ubuntu16.04 + CUDA10.1 + cuDNN7.6.5 + PyTorch1.3.1 + spconv1.2

## pip换源

根据[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/)说明，在终端执行
```
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pip -U
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```
即可

---

## 安装cuDNN
下载[cuDNN](https://developer.nvidia.com/rdp/cudnn-archive),建议下载v7.6.5即可

安装流程参考[官方文档](https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html)

主要是这几步
```
tar -xzvf cudnn-x.x-linux-x64-v8.x.x.x.tgz

sudo cp cuda/include/cudnn*.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn*.h /usr/local/cuda/lib64/libcudnn*
```

对于v7的cuDNN即可直接使用
```
cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
```
检测是否安装成功。v8执行此条命令会输出为空。

---

## 安装PyTorch
安装anaconda可参考[本文](2020-6-2-install-Pytorch.markdown)

新建虚拟环境并进入
```
conda create -n pcdet
conda activate pcdet
```
安装1.3.1的PyTorch，注意对应相应的CUDA版本(使用```nvcc -V```查看)。
**不建议安装更高版本，会和spconv冲突！！！**
```
conda install pytorch==1.3.1 torchvision==0.4.2 cudatoolkit=10.1 -c pytorch
```

之后使用打开python，执行
```python
import torch

torch.empty(5,3)
```
验证torch是否安装正常

---

## 安装spconv(最难的一步了)
下面两部的git clone指令，都可使用[本文](2020-10-18-git-clone.md)来进行加速

### 1
clone [这个版本](https://github.com/traveller59/spconv/tree/468b5713edd3f27493fd35a195458945ade3cef2)的spconv
(本人测试通过)

### 2
对于其包含的```third_party/pybind11```,单独进入```third_party```git clone pybind11。

### 3
```sudo apt-get install libboost-all-dev```

### 4
确保cmake版本>= 3.13.2并已添加到PATH
```
cd ~/Download
wget https://cmake.org/files/v3.18/cmake-3.18.4-Linux-x86_64.tar.gz
tar -xzvf cmake-3.18.4-Linux-x86_64.tar.gz

# 解压出来的包，将其放在 /opt 目录下，其他目录也可以，主要别以后不小心删了
sudo mv cmake-3.18.4-Linux-x86_64 /opt/cmake-3.18.4

# 建立软链接
sudo ln -sf /opt/cmake-3.18.4/bin/*  /usr/bin/

# 查看 cmake 版本
cmake --version
```

### 5
然后进入目录 ```anaconda3/pkgs/pytorch-1.5.1-py3.8_cuda10.1.243_cudnn7.6.3_0/lib/python3.8/site-packages/torch/share/cmake/Caffe2/Caffe2Targets```注释包含```-Wall```的两行```INTERFACE_COMPILE_OPTIONS```
目录具体按照自己安装的版本，不要完全照搬我的！

### 6
执行
```
python setup.py bdist_wheel
```

这一步报错最多，仔细看报错原因。本人碰到
>1.cmake版本太低 
等问题


### 7
```
cd dist

pip install spconv-1.2.1-cp38-cp38m-linux_x86_64.whl
```
第二步根据目录具体按照自己安装的版本，不要完全照搬我的！（建议Tab哈哈哈哈哈

至此，大功告成！
---

## 安装OpenPCDet
```
git clone https://github.com.cnpmjs.org/open-mmlab/OpenPCDet.git

cd OpenPCDet

pip install -r requirements.txt 

python setup.py develop
```

最后一步如果报错```fatal error: cuda.h: No such file or directory.``` 则可以通过在当前虚拟环境下```export C_INCLUDE_PATH=/usr/local/cuda/include```来解决。

---