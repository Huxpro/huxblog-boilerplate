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

小车的驱动电机使用了步进电机(穷就一个字，我只说一次)，而且没有里程计(还不是因为步进电机),只有Kinect2和RPLIDAR，直接导致了在**ROS**导航中使用广泛的gmapping算法无法使用。其间尝试了**cartographer**算法，视觉建图效果极为出色的**ORB_SLAM2**，**VISO2**（编译出错还去改了编译方式），但是由于当时没有意识到SLAM的本质就是一个VO。最后，看到了尝试到本次文章的主角，**RTAB-Map**,其直接就提供VO，而直到我跑通整个框架后才认识到以前那些个算法好像也是可以的。。。菜，我真的是太菜了


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

导航部分是选择这个算法的重要原因（还不是因为自己菜。。。
**SVO**的**ROS**包提供其作为VO节点的输出，配合[robot_pose_efk](http://wiki.ros.org/robot_pose_ekf),理论上可以使用gmapping算法，有兴趣的朋友可以尝试一下。

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

***rgdb_mapping_kinect2.launch***
```xml

<launch>
   
   <!-- Kinect 2
        Install Kinect2 : Follow ALL directives at https://github.com/code-iai/iai_kinect2
                          Make sure it is calibrated!
   -->
   
<!-- 启动kinect２驱动节点　-->            
    <include file="$(find kinect2_bridge)/launch/kinect2_bridge.launch">
        <arg name="base_name"         value="kinect2"/>
        <arg name="sensor"            value=""/>
        <arg name="publish_tf"        value="true"/>
        <arg name="base_name_tf"      value="kinect2"/>
        <arg name="fps_limit"         value="-1.0"/>
        <arg name="calib_path"        value="$(find kinect2_bridge)/data/"/>
        <arg name="use_png"           value="false"/>
        <arg name="jpeg_quality"      value="90"/>
        <arg name="png_level"         value="1"/>
        <arg name="depth_method"      value="default"/>
        <arg name="depth_device"      value="-1"/>
        <arg name="reg_method"        value="default"/>
        <arg name="reg_device"        value="-1"/>
        <arg name="max_depth"         value="12.0"/>
        <arg name="min_depth"         value="0.1"/>
        <arg name="queue_size"        value="5"/>
        <arg name="bilateral_filter"  value="true"/>
        <arg name="edge_aware_filter" value="true"/>
        <arg name="worker_threads"    value="4"/>
    </include>  

<!-- 启动深度图转换激光数据节点 -->
  <node pkg="depthimage_to_laserscan" type="depthimage_to_laserscan" name="depthimage_to_laserscan" output="screen">
    <!--输入图像-->
    <remap from="image" to="/kinect2/qhd/image_depth_rect"/>
    <!--相关图像的相机信息。通常不需要重新变形，因为camera_info将从与图像相同的命名空间订阅。-->
    <remap from="camera_info" to="/kinect2/qhd/camera_info" />
    <!--输出激光数据的话题-->
    <remap from="scan" to="/scan" /> 

    <!--激光扫描的帧id。对于来自具有Z向前的“光学”帧的点云，该值应该被设置为具有X向前和Z向上的相应帧。-->
    <param name="output_frame_id" value="/kinect2_depth_frame"/>
    <!--用于生成激光扫描的像素行数。对于每一列，扫描将返回在图像中垂直居中的那些像素的最小值。-->
    <param name="scan_height" value="30"/>
    <!--返回的最小范围（以米为单位）。小于该范围的输出将作为-Inf输出。-->
    <param name="range_min" value="0.45"/>
    <!--返回的最大范围（以米为单位）。大于此范围将输出为+ Inf。-->
    <param name="range_max" value="8.00"/>
  </node>


   <!-- Which image resolution to process in rtabmap: sd, qhd, hd -->
   <arg name="resolution" default="qhd" />
   
   <!-- Fixed frame id, you may set "base_link" or "base_footprint" if they are published -->
   <arg name="frame_id" default="base_footprint"/>
   
   <!-- Rotate the camera -->
   <arg name="pi/2" value="1.5707963267948966"/>
   <arg name="optical_rotate" value="0 0 0 -$(arg pi/2) 0 -$(arg pi/2)" />
   <node pkg="tf" type="static_transform_publisher" name="kinect2_base_link"
        args="$(arg optical_rotate) kinect2_base_link kinect2_link 100" /> 
  
   <!-- Choose visualization -->
   <arg name="rviz" default="false" />
   <arg name="rtabmapviz" default="true" /> 
   
   <!-- Corresponding config files -->
   <arg name="rtabmapviz_cfg"          default="-d $(find rtabmap_ros)/launch/config/rgbd_gui.ini" />
   <arg name="rviz_cfg"                default="-d $(find rtabmap_ros)/launch/config/rgbd.rviz" />
  
   <!-- slightly increase default parameters for larger images (qhd=720p) -->
   <arg name="gftt_block_size" default="5" /> 
   <arg name="gftt_min_distance" default="5" /> 
        
  <group ns="rtabmap">
  
    <!-- Odometry -->
    <node pkg="rtabmap_ros" type="rgbd_odometry" name="visual_odometry" output="screen">
      <remap from="rgb/image" to="/kinect2/$(arg resolution)/image_color_rect"/>
      <remap from="depth/image" to="/kinect2/$(arg resolution)/image_depth_rect"/>
      <remap from="rgb/camera_info" to="/kinect2/$(arg resolution)/camera_info"/>

      <param name="frame_id" type="string" value="$(arg frame_id)"/>
      <param name="approx_sync" type="bool" value="false"/>
	  
	  <param name="GFTT/BlockSize" type="string" value="$(arg gftt_block_size)"/>
      <param name="GFTT/MinDistance" type="string" value="$(arg gftt_min_distance)"/>
    </node>
  
    <!-- Visual SLAM (robot side) -->
    <!-- args: "delete_db_on_start" and "udebug" -->
    <node name="rtabmap" pkg="rtabmap_ros" type="rtabmap" output="screen" args="--delete_db_on_start">
      <param name="subscribe_depth" type="bool" value="true"/>
	  <param name="frame_id" type="string" value="$(arg frame_id)"/>
	
      <remap from="rgb/image" to="/kinect2/$(arg resolution)/image_color_rect"/>
      <remap from="depth/image" to="/kinect2/$(arg resolution)/image_depth_rect"/>
      <remap from="rgb/camera_info" to="/kinect2/$(arg resolution)/camera_info"/>

      <param name="approx_sync" type="bool" value="false"/>
      
      <param name="GFTT/BlockSize" type="string" value="$(arg gftt_block_size)"/>
      <param name="GFTT/MinDistance" type="string" value="$(arg gftt_min_distance)"/>
    </node>
  
    <!-- Visualisation RTAB-Map -->
    <node if="$(arg rtabmapviz)" pkg="rtabmap_ros" type="rtabmapviz" name="rtabmapviz" args="$(arg rtabmapviz_cfg)" output="screen">
      <param name="subscribe_depth" type="bool" value="true"/>
      <param name="subscribe_odom_info" type="bool" value="true"/>
      <param name="approx_sync" type="bool" value="false"/>
      <param name="frame_id" type="string" value="$(arg frame_id)"/>
    
      <remap from="rgb/image" to="/kinect2/$(arg resolution)/image_color_rect"/>
      <remap from="depth/image" to="/kinect2/$(arg resolution)/image_depth_rect"/>
      <remap from="rgb/camera_info" to="/kinect2/$(arg resolution)/camera_info"/>
    </node>
  
  </group>
  
  <!-- Visualization RVIZ -->
  <node if="$(arg rviz)" pkg="rviz" type="rviz" name="rviz" args="$(arg rviz_cfg)"/>
  <!-- sync cloud with odometry and voxelize the point cloud (for fast visualization in rviz) -->
  <node if="$(arg rviz)" pkg="nodelet" type="nodelet" name="standalone_nodelet"  args="manager" output="screen"/>
  <node if="$(arg rviz)" pkg="nodelet" type="nodelet" name="data_odom_sync" args="load rtabmap_ros/data_odom_sync standalone_nodelet">
    <remap from="rgb/image_in"       to="/kinect2/$(arg resolution)/image_color_rect"/>
    <remap from="depth/image_in"     to="/kinect2/$(arg resolution)/image_depth_rect"/>
    <remap from="rgb/camera_info_in" to="/kinect2/$(arg resolution)/camera_info"/>

    <remap from="odom_in"             to="rtabmap/odom"/>

    <param name="approx_sync" type="bool" value="false"/>
    
    <remap from="rgb/image_out"       to="data_odom_sync/image"/>
    <remap from="depth/image_out"     to="data_odom_sync/depth"/>
    <remap from="rgb/camera_info_out" to="data_odom_sync/camera_info"/>
    <remap from="odom_out"            to="odom_sync"/>
  </node>
  <node if="$(arg rviz)" pkg="nodelet" type="nodelet" name="points_xyzrgb" args="load rtabmap_ros/point_cloud_xyzrgb standalone_nodelet">
    <remap from="rgb/image"       to="data_odom_sync/image"/>
    <remap from="depth/image"     to="data_odom_sync/depth"/>
    <remap from="rgb/camera_info" to="data_odom_sync/camera_info"/>
    <remap from="cloud"           to="voxel_cloud" />

    <param name="voxel_size" type="double" value="0.01"/>
  </node>

  <!-- tf -->
  <node pkg="tf" type="static_transform_publisher" name="base_footprint_to_base_link" args="0 0 0.5 0 0 0 base_footprint base_link 50" />
  <node pkg="tf" type="static_transform_publisher" name="base_link_to_laser" args="0 0 0 0 0 0 base_link laser 50" />
  <node pkg="tf" type="static_transform_publisher" name="base_link_to_kinect2laser" args="0 0 0 0 0 0 base_link kinect2_depth_frame 50" />
  <node pkg="tf" type="static_transform_publisher" name="base_link_to_kinect2_link" args="0 0 0.5 0 0 0 base_link kinect2_base_link 50" />

</launch>

```

move_base的参数文件放在bringup包的nav_config文件夹下

***base_local_planner_params.yaml***
```yaml
TrajectoryPlannerROS:

  # Current limits based on AZ3 standalone configuration.
  acc_lim_x:  0.75
  acc_lim_y:  0.75
  acc_lim_theta: 4
  max_vel_x:  0.5
  min_vel_x: 0.24
  max_vel_theta: 0.5
  min_vel_theta: -0.5
  min_in_place_vel_theta: 0.25
  holonomic_robot: true

  xy_goal_tolerance:  0.25
  yaw_goal_tolerance: 0.25
  latch_xy_goal_tolerance: true
  
  # make sure that the minimum velocity multiplied by the sim_period is less than twice the tolerance on a goal. Otherwise, the robot will prefer to rotate in place just outside of range of its target position rather than moving towards the goal.
  sim_time: 1.5 # set between 1 and 2. The higher he value, the smoother the path (though more samples would be required).
  sim_granularity: 0.025
  angular_sim_granularity: 0.05
  vx_samples: 12
  vtheta_samples: 20

  meter_scoring: true

  pdist_scale: 0.7 # The higher will follow more the global path.
  gdist_scale: 0.8
  occdist_scale: 0.01
  publish_cost_grid_pc: false

#move_base
controller_frequency: 10.0 #The robot can move faster when higher.

#global planner 
NavfnROS:
    allow_unknown: true
    visualize_potential: false

```

***costmap_common_params.yaml***
```yaml
footprint: [[ 0.3,  0.3], [-0.3,  0.3], [-0.3, -0.3], [ 0.3, -0.3]]
footprint_padding: 0.02
inflation_layer:
  inflation_radius: 0.5
transform_tolerance: 2

obstacle_layer:
  obstacle_range: 2.5
  raytrace_range: 3
  max_obstacle_height: 0.4
  track_unknown_space: true

  observation_sources: laser_scan_sensor

  laser_scan_sensor: {
    data_type: LaserScan,
    topic: scan,
    expected_update_rate: 0.5, 
    marking: true,
    clearing: true
  }
```

***global_costmap_params.yaml***
```yaml
global_frame: map
robot_base_frame: base_footprint
update_frequency: 2
publish_frequency: 2
always_send_full_costmap: false
plugins:
   - {name: static_layer, type: "rtabmap_ros::StaticLayer"}
   - {name: obstacle_layer, type: "costmap_2d::ObstacleLayer"}
   - {name: inflation_layer, type: "costmap_2d::InflationLayer"}
```

***local_costmap_params.yaml***
```yaml
global_frame: odom
robot_base_frame: base_footprint
update_frequency: 2
publish_frequency: 2
rolling_window: true
width: 3.0
height: 3.0
resolution: 0.025
origin_x: 0
origin_y: 0

plugins:
  - {name: obstacle_layer,   type: "costmap_2d::ObstacleLayer"}
  - {name: inflation_layer,  type: "costmap_2d::InflationLayer"}
```


***rgbd_navigation_kinect2.launch***
```xml
<launch>
  <arg name="scan_topic" default="/scan"/>
  <arg name="map_topic" default="/rtabmap/grid_map"/>

  <!-- ROS navigation stack move_base -->
  <remap from="scan"             to="$(arg scan_topic)"/>  <!-- see costmap_common_params_2d.yaml -->
  <remap from="map"              to="$(arg map_topic)"/>   <!-- see global_costmap_params.yaml -->
        
  <node pkg="move_base" type="move_base" respawn="true" name="move_base" output="screen">
     <param name="base_global_planner" value="navfn/NavfnROS"/>
     <rosparam file="$(find bringup)/nav_config/costmap_common_params.yaml" command="load" ns="global_costmap" />
     <rosparam file="$(find bringup)/nav_config/costmap_common_params.yaml" command="load" ns="local_costmap" />
     <rosparam file="$(find bringup)/nav_config/local_costmap_params.yaml" command="load" ns="local_costmap" />
     <rosparam file="$(find bringup)/nav_config/global_costmap_params.yaml" command="load" ns="global_costmap"/>
     <rosparam file="$(find bringup)/nav_config/base_local_planner_params.yaml" command="load" />
  </node>
</launch>
```

***rgbd_rviz.launch***
```xml
<launch>
  <node name="rviz" pkg="rviz" type="rviz" args="-d $(find rtabmap_ros)/launch/config/turtlebot_navigation.rviz"/>
</launch>
```


依次启动4个.launch，并启动底盘控制程序和键盘控制程序，即可控制小车移动和建图。 建图完成后，即可通过rviz指定导航目的地。在导航时，建议关闭键盘控制程序，重启底盘控制程序，以防节点冲突。


---

## 后记

本机器人目前只使用了Kinect2一个设备，开发版选用了tx2，性能不错，可以比较流畅得完成建图和导航。但是视觉里程计的精确度和实时性(Kinect2深度转换为雷达大概只能达到2Hz)本来就无法和odom相比。我认为雷达+odom才是室内环境中最好的选择。视觉里程计更适合户外轮子打滑导致odom可信度极低的场景。


---

## 参考博文

[rtabmap_ros 从入门到放弃 + 使用Handsfree进行三维建图导航](https://www.rosclub.cn/thread-25.html)

[rtabmap_ros 使用 kinect2(xbox one)进行SLAM](https://blog.csdn.net/sean_xyz/article/details/65445038)