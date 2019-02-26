---
layout:     post
title:      "ROS小车开发之RTAB-Map的实践"
subtitle:   "基于Kinect2的纯视觉建图和导航"
date:       2019-02-25
author:     "SirJamie"
header-img: "img/post-bg-alita.jpg"
tags:
    - ROS
    - SLAM
    - Linux
---

> 记录一下，写写流水账。写得不对的地方请指出，谢谢！


## Catagory

1. [算法选择](#算法选择)
2. [RTAB-Map](#rtab-map)
3. [建图](#建图)
4. [导航](#导航)
5. [move_base](#move_base)
6. [配置文件](#配置文件)
7. [后记](#后记)
8. [参考博文](#参考博文)


---

## 算法选择

小车的驱动电机使用了步进电机(穷就一个字，我只说一次)，而且没有里程计(还不是因为步进电机),只有Kinect2和RPLIDAR，直接导致了在**ROS**导航中使用广泛的gmapping算法无法使用。而如果选择**Google**的**cartographer**算法，可以得到地图并转化为**ROS**需要的地图格式(yaml)，或者是视觉建图效果极为出色的**ORB_SLAM2**(其也提供ROS包)，但是由于他们都没有VO，故而无法使用**ROS**的**navigation**包中的[robot_pose_efk](http://wiki.ros.org/robot_pose_ekf)，因为其需要VO或者odom作为主输入。其间还尝试了VISO2，但是失败了(忘记什么原因了，反正是不行😂)。最后，看到了
本次文章的主角，**RTAB-Map**,实属救命稻草。


---

## RTAB-Map

[RTAB-Map](http://introlab.github.io/rtabmap/)，提供一个完整的建图导航方案，包括对于视觉输入，轮里程计，lidar三种输入的多种组合方式的解决方案。[文档和教程](http://wiki.ros.org/rtabmap_ros)齐全丰富，[GitHub](https://github.com/introlab/rtabmap_ros/tree/kinetic-devel)，[论坛](http://official-rtab-map-forum.67519.x6.nabble.com/)中也有较多案例和配置，请一定要看！能解决非常多的问题和疑惑。


---

## 建图

相对简单的部分，启动相机(Kincect2的玄学启动真的是服了)，将**RTAB-Map**启动文件的参数对应为相机的rgb输入，深度输入和camera_info,同时记得配置一下tf转换树(这里的tf树只是为了测试方便)，
rtabmap_args:="--delete_db_on_start"的含义是删除掉之前储存的 db 文件，重新建图。

kinect2 bridge
>roslaunch kinect2_bridge kinect2_bridge.launch pulish_tf:=true 

static tf
>rosrun tf static_transform_publisher 0 0 0 -1.5707963267948966 0 -1.5707963267948966 camera_link kinect2_ir_optical_frame  100 

mapping 
>roslaunch rtabmap_ros rgbd_mapping.launch rtabmap_args:="--delete_db_on_start" rgb_topic:=/kinect2/sd/image_color_rect depth_registered_topic:=/kinect2/sd/image_depth_rect camera_info_topic:=/kinect2/sd/camera_info

**注:**
如果使用的是qhd或者hd, 那么对应的frame 是 kinect2_rgb_optical_frame, hd和qhd的尺寸图像都是基于rgd镜头的,所以它们的frame是彩色镜头的frame。而如果使用的是sd, 那么对应的frame是　kinect2_ir_optical_frame, 红外镜头的frame。

建完之后，ctrl+c退出，即可在 ～/.ros 文件夹下看到 一个db文件。

这个文件可以通过**RTAB-Map**自带的工具来查看。

> rtabmap-databaseViewer ~/.ros/rtabmap.db


---

## 导航

导航部分是选择这个算法的重要原因。**SVO**的**ROS**包提供其作为VO节点的输出，配合[robot_pose_efk](http://wiki.ros.org/robot_pose_ekf),理论上可以使用gmapping算法，有兴趣的朋友可以尝试一下。

进行导航其实是用**RTAB-Map**的投影地图或者是scan地图来作为move_base的地图输入，然后利用Navigation Stack进行路径规划等，最后发布/cmd_vel节点。而对于/cmd_vel，最好进行一个平滑处理，使机器人运行流畅。

navigation
>roslaunch rtabmap_ros rgbd_mapping.launch localization:=true rgb_topic:=/kinect2/sd/image_color_rect depth_registered_topic:=/kinect2/sd/image_depth_rect camera_info_topic:=/kinect2/sd/camera_info

启动**RTAB-Map**后，还要启动move_base进行路径规划等(启动文件在后面)。

然后再启动rviz，在rviz中，选择地图topic为/rtabmap/grid_map，全局地图也可以选这个。


---

## move_base

主要是用途是路径规划吧。发现在rviz中画goal却无法导航，才发现是move_base配置不对。以前一直没有关注这个模块，并错误的认为路径规划已经写在了**RTAB_Map**中。

配置文件来自[Autonomous Navigation](http://official-rtab-map-forum.67519.x6.nabble.com/Autonomous-Navigation-td801.html)。这篇帖子内容多多，精华！

配置文件我还会做进一步的调整以适应本机器人的运行。


---

## 配置文件

话不多说，ROS最关键的就是launch文件，大量的参数配置都在其中。下面放上我的launch文件和yaml文件供大家参考。




---

## 后记

本机器人目前只使用了Kinect2一个设备，开发版选用了tx2，性能不错，可以比较流畅得完成建图和导航。但是视觉里程计的精确度和实时性(Kinect2深度转换为雷达大概只能达到2Hz)本来就无法和odom相比。我认为雷达+odom才是室内环境中最好的选择。视觉里程计更适合户外轮子打滑导致odom可信度极低的场景。


---

## 参考博文

[rtabmap_ros 从入门到放弃 + 使用Handsfree进行三维建图导航](https://www.rosclub.cn/thread-25.html)

[rtabmap_ros 使用 kinect2(xbox one)进行SLAM](https://blog.csdn.net/sean_xyz/article/details/65445038)