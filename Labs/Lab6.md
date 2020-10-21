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



```
while True:
  pose = robot.get_pose()
  gt_pose = robot.get_gt_pose()
  robot.send_to_plot(pose[0], pose[1], ODOM)
  robot.send_to_plot(gt_pose[0], gt_pose[1], GT)
  time.sleep(0.5)
 ```

<center><video autoplay loop muted inline width="800"><source src="/ECE4960/assets/videos/lab6/sim.mp4"></video></center>