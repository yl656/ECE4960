---
title: LQG on the Inverted Pendulum on a Cart
description: <a href="https://cei-lab.github.io/ECE4960/Lab11b.html" style="color:#FFCC00;">Lab 12b</a>
layout: default
gif: lab11.gif
---

# Objective

The objective of this lab is to experiment with the Kalman filter and see what effects different modifications on the model (noise, initial state, etc.) has on the inverted pendulum on a cart.

# Simulation

I downloaded the code and compared it with the one from lab 11. New A and B matrices in the Kalman filter version are discretized, and we are not measuring all state variables as the C matrix only has a one on the last column.

I ran the simulation, practically unchanged, with different reference waves. 

<center><img src="/ECE4960/assets/images/lab12/square.png" width="500"><img src="/ECE4960/assets/images/lab12/sawtooth.png" width="500"><img src="/ECE4960/assets/images/lab12/sin.png" width="500"><img src="/ECE4960/assets/images/lab12/random.png" width="500"></center> 

When I run ```control.obsv(P.A,P.C)``` and calculate the rank using ```np.linalg.matrix_rank```, I get a result of 3, which is smaller than 4, the size of our state space, indicating that the system is not observable. Lookaing at matrices A and C as well as the observability matrix, it would seem like \\( x \\), or \\( z \\), would be the state that is not observable. By changing one initial state at a time, we can verify if the system is able to correct itself and therefore deduce which state is not observable.

<center><img src="/ECE4960/assets/images/lab12/initialz.png" width="500"><img src="/ECE4960/assets/images/lab12/initialzd.png" width="500"></center>
<center>\( z \) \( \dot{z} \) $z$ $\theta$ </center>

<center><img src="/ECE4960/assets/images/lab12/initialt.png" width="500"><img src="/ECE4960/assets/images/lab12/initialtd.png" width="500"></center> 
<center>\( \theta \) \( \dot{\theta} \)</center>