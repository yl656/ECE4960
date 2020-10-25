---
title: IMU and PID
description: <a href="https://cei-lab.github.io/ECE4960/Lab6.html" style="color:#FFCC00;">Lab 6</a>
layout: default
gif: lab6.gif
---

# Objective

The next step in feedback control is to determine where our robot is, and what direction is facing. We can achieve this by setting up the IMU which contains an accelerometer, a gyroscope, and a magnetometer. Using data from these sensors, we can determine the approximate odometry of the robot.

# Tasks

## A. Physical Robot

### IMU

As per the instructions, I installed the library for the IMU and scanned the I2C address of the IMU, and it's 0x69 as expected. When running the example script, I tried to apply acceleration on all three axes of the accelerometer as well as all three axes of the gyroscope.

<center><video controls width="800"><source src="/ECE4960/assets/videos/lab6/sim.mp4"></video></center>

As we can see, I was oscillating the IMU first on the accelerometer's X, Y, Z axes and then on the gyroscope's Z, X, Y axes, in that order with each axis specified by a color on top. The accelerometer outputs don't have a maximum range that my arms produced but the outputs from the gyroscope are capped at around 250.

#### Accelerometer

#### Gyroscope

#### Magnetometer

### PID

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

