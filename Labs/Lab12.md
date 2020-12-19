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
<center>left: \( z \) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; right: \( \dot{z} \)</center>

<center><img src="/ECE4960/assets/images/lab12/initialt.png" width="500"><img src="/ECE4960/assets/images/lab12/initialtd.png" width="500"></center> 
<center>left: \( \theta \) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; right: \( \dot{\theta} \)</center>

As we can see from the above plots, if $z$ is off, there is no way to correct it given our current system and the offset persists. Therefore, it is the state that is not observable. When the other states have an offset, they can either be directly or indirectly adjusted. For example, when $\theta$ has a different initial value, the cart is able to lift the pendulum back to upright position.

<center><video autoplay loop muted inline width="600"><source src="/ECE4960/assets/videos/lab12/init.mp4"></video></center>

Next, to have a more realistic estimate of how well Kalman filter works, I added imperfections step by step.

First, some random noise are added to all four initial states as discrepencies. The Kalman filter seems to be able to adjust very quickly, even though it can't get rid of the offset in $z$. The noise I chose for $z$ and $\dot{z}$ are $\pm0.5$. For $\theta$, $\pm20\circ$ and for $\dot{\theta}$, $\frac{\pi}{2}rad/s$. These are all relatively high errors and the real offsets are unlikely to exceed the range.

<center><video autoplay loop muted inline width="600"><source src="/ECE4960/assets/videos/lab12/init4.mp4"></video></center>

<center><img src="/ECE4960/assets/images/lab12/init.png" width="600"></center> 

For the deadband and saturation, we know from lab 6 that the cart has a maximum velocity of $2.75m/s$. For the minimum velocity, I decided on $0.2m/s$ because it seems resonable. Therefore, if the absolute value of $u$ is above 2.75, it is capped. If it is below 0.2, it is set to 0.

<center><video autoplay loop muted inline width="600"><source src="/ECE4960/assets/videos/lab12/deadsat.mp4"></video></center>

<center><img src="/ECE4960/assets/images/lab12/deadsat.png" width="600"></center> 

The overall balancing of the pendulum seems to be working fine, but the $z$ position unfortunately fails to be updated correctly. This is probably due to the deadband region. The cart tries to do tiny changes to adjust the position but the deadband region does not allow it to happen. Movements are therefore more jerky.