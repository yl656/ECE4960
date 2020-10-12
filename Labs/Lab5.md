---
title: Obstacle Avoidance
description: <a href="https://cei-lab.github.io/ECE4960/Lab5.html" style="color:#FFCC00;">Lab 5</a>
layout: default
gif: lab5.gif
---

# Objective

The first and arguably the most essential part of feedback control is obstacle avoidance. After all, we don't want this to happen.

![](../assets/images/lab51.gif)

Therefore, by adding two sensors to our robot, the robot will be able to sense if anything is in front of it and actively avoid it, either stopping completely or turning away. We also conduct similar experiments in the simulator, but the virtual robot differs from the physical robot in two ways. In the simulator, there is no acceleration, and the robot is very rigid. 

# Tasks

## A. Physical Robot

### Proximity Sensor

Following the instructions online, I installed the library, connected, and found the address of the VCNL4040 proximity sensor. It has an address of 0x60, which is its default hard-wired address, as stated in the datasheet.

Using the grid, I was able to conduct experiments mapping the actual distance of objects to the "proximity value," as seen below, with the left one on linear scale and the right one on log scale.

<img align = "left" src="/ECE4960/assets/images/lab5/prox_lin.png" width=500> <img align = "right" src="/ECE4960/assets/images/lab5/prox_log.png" width=500>

I tested different materials with different colors. Gray and white both refer to the piece of paper for calibration given to us, while reflective is the piece of hard paper from the bluetooth adapter that reflects visible lights better. As we can see, the proximity value depends on not only the distance but also the material. Its resolution also degrades rapidly for objects merely 10cm away. Therefore, this sensor might not be the best fit for our fast robot.

### Distance Sensor

Similarly, I tested the I2C address (0x29, as expected) and calibrated the sensor using the piece of 17% gray paper given to us. I then tested the acccuracy of the time-of-flight sensor by measuring distances that are already measured by a tape measure.

<center><img src="/ECE4960/assets/images/lab5/tof.png" width="500"></center> 

The results are obtained with two surfaces, my bathroom door which is wood, and my kitchen wall. Obviously, the sensor produces higher values for both sensors, but there the results from different surfaces appear quite consistent. Therefore, we can simply apply a scaling factor to convert the sensor data to actual distance.

### Assembly & Integration

For the "roomba", I don't believe that the proximity sensor will be of much use. The proximity sensor has a better resolution when the object is only a few centimeters away, but a few centimeters from where the sensors are mounted might not even reach the outer border of the robot. Besides, since our robot is going to be fast, a crash is bound to happen if we pick up something a few centimeters away. Therefore, only the distance sensor will be required.

I plan to explore the quickest way to stop the robot completely. One way to stop it will be simply setting the velocity to 0. This is slow but safe. Another way is to put the motors in full reverse, which will definitely result in a flip. Therefore, I need to find a (presumably small) negative velocity that I could apply to the motors so that I could decelerate the robot as soon as possible, and then bring the velocity up to 0.

Then, everything is simple. All we have to do is have the robot driving in full velocity, and when our time-of-flight distance sensors detect a distance that is slightly above our braking distance, we come to a full stop and spin to a different direction. Since route-planning is not in the scope of this lab, we can choose a random direction with no obstacle and repeat the process.

Unfortunately, technical difficulties stopped that from happening. The qwiic part of my Artemis board seems to have stopped working completely as I am not able to detect any I2C devices that are connected at the moment. However, I plan to try using the other 2 I2C pins on the board after some soldering and see if that fixes the issue. This will be updated as soon as the problem is resolved.

## B. Virtual Robot

At first, I didn't think that there was a duration for velocity commands, as they seem to go on forever. However, I accidently found out that they stop after a while without knowing when it stopped exactly. After staring at the virtual robot spin and falling asleep once or twice, I decided to record my screen and determine exactly how long the commands last. Turns out it's five minutes. That information, however, is not that useful unless we switch to a much bigger map that allows our robot to travel for five minutes before detecting anything in front of it.

Since acceleration does not exist in the simulator, we can always travel at max speed, which is 1. Unfortunately we cannot go any higher because of the velocity limitations.

In order for the virtual robot to detect obstacle, we need to make use of the ```get_laser_data()``` function, which returns the distance (maximum distance 6.0) from the "range finder" with noise. To understand the characteristics of the noise better, I collected 1000 data points on a stationary robot, each 0.1s apart, and plotted the distribution.

<center><img src="/ECE4960/assets/images/lab5/distribution.png" width="500"></center> 

As we can see from plot, the distribution is somewhat Gaussian with \\( \mu \approx 1.14 \\). Most of the data points fall between 0.95 and 1.36, which gives a range of approximately \\( \mu \pm 0.2 \\). Now that we understand the noise better, we can account for that in our algorithm for obstacle avoidance.

```
while True:
  idx = 0
  robot.set_vel(1,0)
  hist = 0
  while robot.get_laser_data() > 0.3:
		time.sleep(0.02)
	    if idx == 0:
        hist = robot.get_laser_data()
	    idx = idx + 1
	    if idx == 50:
        if abs(robot.get_laser_data()-hist) < 0.5 and hist != 6.0:
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

We can actually see that the robot got stuck at the corner at about 0:18, and that the robot is able to free itself and return to normal. However, that is still considered a collision. In order to avoid collision completely, the robot cannot travel in a straight line as the limited detection angle will not be able to see corners or walls in parallel. To fix it, we either need to add more sensors, or spin a little so we can cover a larger angle.