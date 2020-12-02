---
title: Planning
description: <a href="https://cei-lab.github.io/ECE4960/Lab10.html" style="color:#FFCC00;">Lab 10</a>
layout: home
gif: lab10.gif
---

# Objective

The objective of this lab is to perform navigation from a random start point to a random end point by combining Bug algorithms (and other local/global algorithms) for path planning and and Bayes/odomtery for localization. As described in my lab 9 report, the restrictions in my apartment hinders the accuracy of my localization, therefore I decided to do this lab completely in the virtual simulator and create a much more interesting geometry that hopefully increases the accuracy of my Bayes filter.

# Simulation

Since I am using the simulator only, I created a much more insteresting map loosely based on my apartment.

<center><img src="/ECE4960/assets/images/lab10/map.png" width="500"><img src="/ECE4960/assets/images/lab8/bayes.png" width="500"></center>

With two new trajectories, I tested out how good Bayes localization is.

<center><img src="/ECE4960/assets/images/lab10/loc1.png" width="500"><img src="/ECE4960/assets/images/lab8/bayes.png" width="500"></center>

<center><img src="/ECE4960/assets/images/lab10/loc2.png" width="500"><img src="/ECE4960/assets/images/lab8/bayes.png" width="500"></center> 

As we can see, the localization works relatively well, but there are still some outliers. 

## Occupancy Matrix

A way to represent my map is to convert it to an occupancy matrix with 1 representing the obstacles. To represent the robot as a point, proper clearance from the obstacles is also required. Therefore, I created an occupancy matrix with each grid representing a 0.1mx0.1m square with padding/clerance set to 0.3m. When converting line segments into occupany matrices, I simply find the distances between the line segment and the blocks nearby and set the ones below the threshold to 1. Below is the occupancy matrix generated with a random start point and a random goal.

<center><img src="/ECE4960/assets/images/lab10/occu.png" width="500"><img src="/ECE4960/assets/images/lab8/bayes.png" width="500"></center> 

## Bug 2

## BFS

## Dijkstra

