---
title: Obstacle Avoidance
description: <a href="https://cei-lab.github.io/ECE4960/Lab5.html" style="color:#FFCC00;">Lab 5</a>
layout: default
gif: lab5.gif
---

# Objective

The first and arguably the most essential part of feedback control is obstacle avoidance. After all, we don't want this to happen.

![](../assets/images/lab51.gif)

Therefore, by adding two sensors, ...


# Tasks

## A. Physical Robot

## B. Virtual Robot

In order for the virtual robot to detect obstacle, we need to make use of the ```get_laser_data()``` function, which returns the distance (maximum distance 6.0) from the "range finder" with noise. To understand the characteristics of the noise better, I collected 1000 data points on a stationary robot, each 0.1s apart, and plotted the distribution.

<center><img src="/ECE4960/assets/images/lab5/distribution.png" width="500"></center> 

As we can see from plot, the distribution is somewhat Gaussian with \\( \mu \approx 1.14 \\). Most of the data points fall between 0.95 and 1.36, which gives a range of approximately \\( \mu \pm 0.2 \\). Now that we understand the noise better, we can account for that in our algorithm for obstacle avoidance.

```
while True:
  idx = 0
  robot.set_vel(1,0)
  hist=[0,0]
  while robot.get_laser_data() > 0.3:
		time.sleep(0.02)
	    if idx == 0:
        hist = robot.get_laser_data()
	    idx = idx + 1
	    if idx == 50:
        if abs(robot.get_laser_data()-hist)<0.5 and hist != 6.0:
          break
        else:
          idx = 0
  dur = random.uniform(1,3)
  dire = random.random()
  if (dire < 0.45):
      robot.set_vel(-0.05, 2)
  elif (dire < 0.9):
      robot.set_vel(-0.05, -2)
  elif (dire < 0.95):
      robot.set_vel(-1, 0.5)
  else:
      robot.set_vel(-1, -0.5)
  time.sleep(dur)
```

First, the robot travels straight. Whenever the laser data drops below 0.3, which we know can be anything from 0.1 to 0.5 in the simulator, we have to change the course of our robot. Usually, that can be fixed by spinning in place. Therefore, we can randomly choose a direction and spin from 90 degrees to 270 degrees, which may or may not get us away from that obstacle. If not, we can always try again. We also assign a small negative linear velocity to slightly help the robot move away from the obstacle. 

In rare circumstances, turning in place cannot fix the problem. For example, in the image below, there is not place for the robot to spin clockwise or counterclockwise. Therefore, two more actions are introduced. For a 10% chance, the robot might back up with a random direction. Since we check the robot every second, the robot can get out of this situation in a few seconds.

<center><img src="/ECE4960/assets/images/lab5/error1.png" width="400"></center> 

Getting stuck at corners is also something quite rare. As we can see from the picture below, the robot is stuck but the range finder does not know due to its small detection angle. Therefore, our program also checks whether the range finder data has changed in the past second. Again, from the distributino plot above, we know that the maximum difference between two signals when the robot is stuck should be about 0.4, which is why the threshold is set at 0.5 just in case.  

<center><img src="/ECE4960/assets/images/lab5/error2.png" width="400"></center> 

With these different scenarios in mind, the robot wanders on the map endlessly, or at least for half an hour or so before I shut it off, without getting stuck. Below is a short segment from the run.

<center><video controls width="800"><source src="/ECE4960/assets/videos/lab5/simulation.mp4"></video></center>

We can actually see that the robot got stuck parallel to the wall at about 0:12 and at the corner at about 0:18. In both scenarios, the robot is able to free itself and return to normal.