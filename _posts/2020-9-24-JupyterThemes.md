---
layout:     post
title:      "给Jupyter Notebook换主题"
subtitle:   ""
date:       2020-9-24
author:     "SirJamie"
header-img: "img/head-post/post-bg-jupyterthemes.jpg"
tags:
    - Mac
    - Python
---

## 使用pip install 安装

```sh
# install jupyterthemes
pip install --user jupyterthemes

# upgrade to latest version
pip install --upgrade jupyterthemes
```

安装完之后 ``cd ~/.local/bin`` 
然后 ``jt -l`` 查看想要安装的主题

我本人选择了iTerm2相同的主题 solarizedd
使用命令``jt -t solarizedd``切换

要是都不喜欢就``jt -r``换回默认的

---

## 调整plot样式

```py
# import jtplot module in notebook
from jupyterthemes import jtplot

# choose which theme to inherit plotting style from
# onedork | grade3 | oceans16 | chesterish | monokai | solarizedl | solarizedd
jtplot.style(theme='solarizedd')

# set "context" (paper, notebook, talk, poster)
# scale font-size of ticklabels, legend, etc.
# remove spines from x and y axes and make grid dashed
jtplot.style(context='talk', fscale=1.4, spines=False, gridlines='--')

# turn on X- and Y-axis tick marks (default=False)
# turn off the axis grid lines (default=True)
# and set the default figure size
jtplot.style(ticks=True, grid=False, figsize=(6, 4.5))

# reset default matplotlib rcParams
jtplot.reset()
```
然后在```~/.ipython/profile_default/startup/startup.ipy```文件中添加如下字段，这样每次打开就用新的plot样式替换默认的plot了

```py
# import jtplot submodule from jupyterthemes
from jupyterthemes import jtplot

# currently installed theme will be used to
# set plot style if no arguments provided
jtplot.style()
```

---
