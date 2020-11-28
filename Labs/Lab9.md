---
title: Planning
description: <a href="https://cei-lab.github.io/ECE4960/Lab9.html" style="color:#FFCC00;">Lab 9</a>
layout: default
gif: lab9.gif
---

# Objective

Using the provided Bayes filter implementation, we try to perform actual localization using the real robot.

# Simulation

First, I wanted to try the provided implementation and see if it performs differently compared to my own implementation. The provided implementation is shown on the left and my own on the right for reference.

<center><img src="/ECE4960/assets/images/lab9/old.png" width="500"><img src="/ECE4960/assets/images/lab8/bayes.png" width="500"></center> 

As we can see, both do a passable job when it comes to localization for this particular map gemotry. Before I start gathering observation data from the real world, however, I wanted to check how the filter performs on the actual layout of my room.

<center><img src="/ECE4960/assets/images/lab9/updatedmap.png" width="500"></center> 

First, I replaced the prediction step with ```init_uniform_distribution()``` so that the prior belief (bel_bar) is a uniform distribution. Therefore, for every step, only update step is calculated.

<center><img src="/ECE4960/assets/images/lab9/simonlyupdate.png" width="500"></center> 

The result is quite disappointing. The only two points that got close are the origin and the one on the top. I then tried to incorporate the prediction step back.

<center><img src="/ECE4960/assets/images/lab9/simpredupdate.png" width="500"></center> 

Similarly, the only points that the Bayes filter got right was the first two and the last two points. The map geometry is ultimately to blame here. It is too simple and the bounding box for the simulator definitely did not help. However, if I enlarge the bounding box to create an asymmetry, maybe the result will improve. This turns out to be a fool's errand.

<center><img src="/ECE4960/assets/images/lab9/biggerbox.png" width="500"></center>

In the image above, the bounding box is set to a 3x3 box (not shown on the plotter). The result is very similar to the ones before. I also tested different sizes and it doesn't help. The ones that are more accurate than others are near the origin and in the hallway away from the entrance (marked in the map below). The problem is that the hallway is covered by carpet, which means that the robot cannot really spin to gather distances at different angles. Therefore, I am not optimistic about real-world localization.

<center><img src="/ECE4960/assets/images/lab9/anno.png" width="500"></center>