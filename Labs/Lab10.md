---
title: Planning
description: <a href="https://cei-lab.github.io/ECE4960/Lab10.html" style="color:#FFCC00;">Lab 10</a>
layout: home
gif: lab10.gif
---

# Objective

The objective of this lab is to perform navigation from a random start point to a random end point by combining algorithms for path planning and and Bayes/odomtery for localization. As described in my lab 9 report, the restrictions in my apartment hinders the accuracy of my localization, therefore I decided to do this lab completely in the virtual simulator and create a much more interesting geometry that hopefully increases the accuracy of my Bayes filter.

# Simulation

Since I am using the simulator only, I created a much more insteresting map loosely based on my apartment.

<center><img src="/ECE4960/assets/images/lab10/map.png" width="500"></center>

With two new trajectories, I tested out how good Bayes localization is.

<center><img src="/ECE4960/assets/images/lab10/loc1.png" width="500"></center>

<center><img src="/ECE4960/assets/images/lab10/loc2.png" width="500"></center> 

As we can see, the localization works relatively well, but there are still some outliers. 

## Occupancy Matrix

A way to represent my map is to convert it to an occupancy matrix with 1 representing the obstacles. To represent the robot as a point, proper clearance from the obstacles is also required. Therefore, I created an occupancy matrix with each grid representing a 0.1mx0.1m square with padding/clerance set to 0.3m. When converting line segments into occupany matrices, I simply find the distances between the line segment and the blocks nearby and set the ones below the threshold to 1. Below is the occupancy matrix generated with two random start points and two random goals. We will focus on these two because they present some interesting challenges.

<center><img src="/ECE4960/assets/images/lab10/occu1.png" width="500"><img src="/ECE4960/assets/images/lab8/occu2.png" width="500"></center> 

I explored two different algorithms in terms of time complexity and space complexity.

## BFS

BFS assumes that all costs are equal. Therefore, the only logical moves are moving to the blocks directly adjacent to the current block.

```
def bfs(grid, start_cell, end_cell):
    frontier = [start_cell]
    parents = {}
    max_size = 0
    it1 = 0
    it2 = 0
    while(frontier):
        it1 = it1 + 1
        max_size = max(len(frontier),max_size)
        curr_node = frontier[0]
        frontier = frontier[1:]
        if (curr_node[0] == end_cell[0] and curr_node[1] == end_cell[1]):
            break
        for i in [(-1,0),(1,0),(0,-1),(0,1)]:
            new_node = curr_node + i
            if (new_node[0],new_node[1]) in parents or grid[new_node[0],new_node[1]] == 1:
                continue
            it2 = it2 + 1
            frontier.append(new_node)
            parents[(new_node[0],new_node[1])] = curr_node
    curr_node = end_cell
    backtrace = []
    while(curr_node[0] != start_cell[0] or curr_node[1] != start_cell[1]):
        backtrace.append(curr_node)
        curr_node = parents[(curr_node[0],curr_node[1])]
    print("max length of frontier is {}".format(max_size))
    print("it1 is {}".format(it1))
    print("it2 is {}".format(it2))
    return backtrace[::-1]
```

<center><img src="/ECE4960/assets/images/lab10/bfs1.png" height="300"><img src="/ECE4960/assets/images/lab8/bfs2.png" height="300"><img src="/ECE4960/assets/images/lab8/bfs3.png" height="300"></center> 

As we can see, BFS indeed finds the optimal Manhattan distance path. I also printed out some key stats, including the maximum frontier size, the amount of iterations, and the execution time averaged over five searches. The execution time is surprisingly good, likely because the frontier hits the obstacles and limits its size. Therefore, the algorithm does not have to search through a lot of nodes. The algorithm also does not have to do redundant work because a history of visited nodes is kept. This also reduces the memory required as the maximum length of all these different frontiers is only 24 (with the increased resolution).

| Index 	| 1 	| 2 	| 3 	|
|:-:	|:-:	|:-:	|:-:	|
| Max Frontier 	| 13 	| 24 	| 20 	|
| Iterations Big Loop 	| 289 	| 609 	| 680 	|
| Iterations Small Loop 	| 299 	| 612 	| 681 	|
| Avg. Exec Time (s) 	| 0.012 	| 0.024 	| 0.035 	|
| Path Length (m) 	| 5 	| 4.8 	| 9.1 	|


## Dijkstra

