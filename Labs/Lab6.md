---
title: IMU and PID
description: <a href="https://cei-lab.github.io/ECE4960/Lab6.html" style="color:#FFCC00;">Lab 6</a>
layout: default
gif: lab6.gif
---

# Objective



# Tasks

## A. Physical Robot

## B. Virtual Robot

For this part of the lab, we are trying to see how accurate the odometry actually is. We first set up the lab like all previous labs, then we launch three tools: the simulator, the plotter, and the keyboard teleoperation tool. Then, we plot the location (noisy, untrusted odom as well as the ground truth) of the virtual robot on the plotter continuously while controlling the virtual robot using the teleoperation tool.

```
while True:
  pose = robot.get_pose()
  gt_pose = robot.get_gt_pose()
  robot.send_to_plot(pose[0], pose[1], ODOM)
  robot.send_to_plot(gt_pose[0], gt_pose[1], GT)
  time.sleep(0.5)
 ```

The poses are sampled at 2Hz to prevent overcrowding the plotter. Here's what the plotted looks like when driving around.

<center><video autoplay loop muted inline width="800"><source src="/ECE4960/assets/videos/lab6/sim.mp4"></video></center>

As we can see, the odometry data and the ground truth are somewhat close to each other at the beginning, with the ground truth matching the trajectory exactly (of course). However, as time goes by, they are farther and farther from each other as the noise/error in the odometry accumulates over time. By the end, they are off by at least a few meters.

I also tested the robot in several other conditions. For example, when it is staying completely still.

