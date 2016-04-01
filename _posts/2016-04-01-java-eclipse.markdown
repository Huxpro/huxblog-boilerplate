---
layout:     post
title:      "一篇文章搞定Java开发环境搭建"
subtitle:   "Windows，Linux通吃"
date:       2016-04-01
author:     "Bebop.C"
header-img: "img/post-java-eclipse.jpg"
tags:
    - Java
    - Eclipse
---


**版权声明：欢迎分享，转载请注明出处。**

---

## 内容概览

- Windows环境 jdk安装、配置，Eclipse安装
- Linux环境 jdk安装、配置，Eclipse安装、启动器制作


## Windows环境

### 安装JDK

进入Oracle官网下载[Java SE JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)，如果你的系统是32位，选择Windows x86版本；64位则选择Windows x64版本。

下载完成后双击文件安装。

### 配置JDK环境变量

> 以win8.1为例，其他常见winidows系统大同小异。

1.右键点击“这台电脑”，选择“属性”，点击“高级系统设置”，点击“环境变量”。

*在下面“系统变量”框里做更改可以对所有本计算机用户生效。*

2.点击“新建”，在“变量名”处输入 `JAVA_HOME` ，“变量值”处填写你的JDK安装路径，例如 `C:\Program Files\Java\jdk1.8.0_45` ，点击“确定”。

3.在“系统变量”框中找到变量 `CLASSPATH` ，双击打开，在“变量值”处增加以下内容 `;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar` 。

*如果没有这个变量则自己“新建”一个，在“变量值”处填写以下内容 `.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar` ，注意最前面有个点。*

4.同样在“系统变量”框中找到变量 `Path` ，双击打开，在“变量值”处增加以下内容 `;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin` 。

*如果没有这个变量也需自己“新建”一个，在“变量值”处填写以下内容 `.;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin` ，同样注意最前面有个点。*

5.点击两次“确定”退出设置。

### 检验是否配置成功

1.打开cmd终端（按win+R快捷键打开“运行”，输入cmd，确定），输入命令:

```
java -version
```
看到以下版本信息
![java-version](img/in-post/post-java-eclipse/java-version.jpg)

2.再输入命令:

```
javac
```
出现以下javac命令使用方法介绍
![javac](img/in-post/post-java-eclipse/javac.jpg)

恭喜配置成功。如果不成功，请查验自己的环境变量是否正确配置。

### 安装Eclipse

进入[Eclipse官网](http://www.eclipse.org/downloads/)根据系统和开发对象选择相应安装程序下载。



## Linux环境

### 下载JDK

1.进入Oracle官网下载[Java SE JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)，选择对应你的Linux发行版的压缩包，例如我的是Ubuntu 64 bits，则下载`jdk-8u73-linux-x64.tar.gz` 。

2.解压文件

```
$ tar zxvf jdk-8u73-linux-x64.tar.gz
```

将解压出来的文件夹放到你期望的路径

```
$ sudo mv jdk1.8.0_73 /opt/jdk1.8.0_73
```

### 配置JDK环境变量

配置内容可以写在 `~/.bashrc` ，但建议写在 `~/.profile` 里，这样在登录时就会自动设置好环境，而不是打开终端的时候才设置。

*这样设置有利于下文介绍的Eclipse启动器的使用。*

用你喜欢的编辑器编辑 `~/.profile` 文件，在最后加入

```
JAVA_HOME=/opt/jdk1.8.0_73
PATH=$PATH:$JAVA_HOME/bin
CLASSPATH=$CLASSPATH:$JAVA_HOME/lib
export JAVA_HOME
export PATH
export CLASSPATH
```
*注意：在JAVA_HOME=后面填写你自己的jdk路径。*


### 下载Eclipse

1.进入[Eclipse官网](http://www.eclipse.org/downloads/)根据系统和开发对象选择相应的压缩包下载。

2.同下载JDK时一样，解压并将其放到你期望的路径，例如

```
$ sudo mv eclipse /opt
```

### 配置Eclipse运行环境

这部分可参考[linux 下 eclipse 开发环境的搭建](http://www.cnblogs.com/jerryzong/archive/2012/03/21/2410501.html) 

本文介绍另一种直接建立启动器的方式来运行Eclipse。

### Ubuntu环境下制作Eclipse启动器

创建一个 `eclipse.desktop` 文件并放到 `/usr/share/applications`

```
$ sudo gedit /usr/share/applications/eclipse.desktop
```

填入以下内容并保存

```
[Desktop Entry]
Name=Eclipse
Type=Application
Exec=/opt/eclipse/eclipse
Terminal=false
Icon=/opt/eclipse/icon.xpm
Comment=Integrated Development Environment
NoDisplay=false
Categories=Development;IDE
Name[en]=eclipse.desktop
```
若你的系统版本为Ubuntu 13.10，还需在最后加入一行

```
Exec=env UBUNTU_MENUPROXY=0 /opt/eclipse/eclipse
```
将创建好的Eclipse图标拖到启动栏即可。



## 参考文献
[1][linux 下 eclipse 开发环境的搭建](http://www.cnblogs.com/jerryzong/archive/2012/03/21/2410501.html)  
[2][Installing Eclipse to Ubuntu 13.10, 13.04, 12.04](http://www.blogs.digitalworlds.net/softwarenotes/?p=54)  