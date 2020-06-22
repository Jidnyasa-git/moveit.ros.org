---
author: Jorge Nicho, ROS-Industrial
comments: false
date: 2020-06-09
layout: post
title: MoveIt 2 Used on Collaborative Robotic Sanding Application
media_type: video
media_link: https://www.youtube.com/embed/5cBrwU9o5JA
description: The ROS-Industrial team leveraged MoveIt 2 for their Collaborative Robotic Sanding Application.
categories:
- industrial
- moveit2
- ur5
---

Cross-posted from <a href="https://rosindustrial.org/news/2020/4/29/using-moveit2-on-a-industrial-open-source-application" target="_blank">ROS-Industrial blog</a>

The Motion Planning Framework for ROS known as [MoveIt](/) has been successfully used in numerous industrial and research applications where complex collision-free robot motions are needed in order to complete manipulation tasks. In recent months, a great deal of effort has gone into migrating MoveIt into ROS2 and as a result the new MoveIt2 framework already provides access to many of the core features and functionality available in its predecessor. While some of the very useful setup tools are still a work in progress (Mainly the MoveIt setup assistant), I was able to integrate MoveIt2 into the <a href="https://github.com/swri-robotics/collaborative-robotic-sanding" target="_blank">Collaborative Robotic Sanding Application (CRS)</a> in order to plan trajectories which were then executed on a <a href="http://gazebosim.org/" target="_blank">Gazebo</a> simulated UR10 robot arm.

My ROS2 setup involved building the MoveIt2 repository from source as described in github and then overlaying that colcon workspace on top of my existing CRS application workspace. I also built and ran the simple demo which worked right out of the gate and was very helpful in helping me understand how to integrate MoveIt2 into my own application.

The C++ integration was very straight forward and only needed the use of two new classes, MoveItCpp and PlanningComponent. In this architecture, MoveItCpp is used to load the robot model, configure the planning pipeline from ROS2 parameters and initialize defaults; then there's the PlanningComponent class which is associated to a planning group and is used to setup the motion plan request and call the low level planner. Furthermore, the PlanningComponent class has a similar interface to the familiar <a href="http://docs.ros.org/melodic/api/moveit_ros_planning_interface/html/classmoveit_1_1planning__interface_1_1MoveGroupInterface.html" target="_blanl">MoveGroupInterface</a> class from MoveIt; however one of the big changes here is that the methods in the PlanningComponent class aren't just wrappers to various services and actions provided by the [move_group](/documentation/concepts/) node but they instead make direct function calls to the various motion planning capabilities. I think this is a welcomed changed since this architecture will allow creating MoveIt2 planning configuration on the fly that can adapt to varying planning situations that may arise in an application.

On the other hand, the launch/yaml integration wasn't as clean as many ROS2 concepts are still relatively new to me. In order to properly configure MoveIt2, it is necessary to load a <a href="http://wiki.ros.org/urdf" target="_blank">URDF</a> file as well as a number of parameters residing in several yaml files into your MoveIt2 application. Fortunately, most of the yaml files generated by the <a href="https://ros-planning.github.io/moveit_tutorials/doc/setup_assistant/setup_assistant_tutorial.html" target="_blank">MoveIt Setup Assistant</a> from the original MoveIt can be used with just minor modifications and so I ran the Setup Assistant in ROS1 and generated the needed config files. Furthermore, the ability to assemble ROS2 launch files in python really came in handy here as it allowed me to instantiate a python dictionary from a YAML file and pass its elements as parameters for my ROS2 application. Beyond learning about MoveIt2, going through this exercise showed me how to reuse the same yaml file for initializing parameters in different applications which I thought was a feature that was no longer available in ROS2.

My overall impression of MoveIt2 was very positive and I feel that the architectural changes aren't at all disruptive to existing MoveIt developers and furthermore it'll lead to new interesting ways in which the framework gets used; I sure look forward to the porting of other very useful MoveIt components. The branch of project that integrates MoveIt2 can be found <a href="https://github.com/swri-robotics/collaborative-robotic-sanding/tree/moveit2-integration-test" target="_blank">here</a> and below is a short clip of the planning that I was able to do with it. In this application, the robot has to move the camera to three scan position and so MoveIt2 is used to plan collision-free motions to those positions.