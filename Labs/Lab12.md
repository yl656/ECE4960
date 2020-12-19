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

First, some random noise are added to all four initial states as discrepencies. The Kalman filter seems to be able to adjust very quickly, even though it can't get rid of the offset in $z$. The variance I chose for $z$ and $\dot{z}$ are $0.5$. For $\theta$, $20\circ$ and for $\dot{\theta}$, $\frac{\pi}{2}rad/s$. These are all relatively high errors and the real offsets are unlikely to exceed the range.

<center><video autoplay loop muted inline width="600"><source src="/ECE4960/assets/videos/lab12/init4.mp4"></video></center>

<center><img src="/ECE4960/assets/images/lab12/init.png" width="600"></center> 

For the deadband and saturation, we know from lab 6 that the cart has a maximum acceleration of about $3m/s^2$. For the minimum acceleration, I decided on $0.2m/s^2$ because it seems resonable. This is honestly not great because drag is ignored.

<center><video width="600"><source src="/ECE4960/assets/videos/lab12/deadsat.mp4"></video></center>

<center><img src="/ECE4960/assets/images/lab12/deadsat.png" width="600"></center> 

The overall balancing of the pendulum seems to be working fine, but the $z$ position unfortunately fails to be updated correctly. This is probably due to the deadband region. The cart tries to do tiny changes to adjust the position but the deadband region does not allow it to happen. Movements are therefore more jerky.

To add process noise, only a single line in the dynamic model has to be uncommented.

<center><video width="600"><source src="/ECE4960/assets/videos/lab12/process.mp4"></video></center>

<center><img src="/ECE4960/assets/images/lab12/process.png" width="600"></center> 

The process noise does not seem to make a lot of difference. Measurement noise is then accomplished by adding random values to the state values, or ```old_state```, that are multiplied by matrix C at every iteration. As expected, the measurement noise has a huge impact on the Kalman filter result and I had to lower my original variance by a lot. I also found out that my initial state discrepency might have too big of an impact as all the modifications after it has a significant chance of breaking the system especially with the measurement noise added so I lowered the variance by 75%. Shown below is the result of a broken controller.

<center><img src="/ECE4960/assets/images/lab12/process.png" width="600"></center> 

The cart just goes further and further to the right and never comes back. After adjusting, however, the system is a lot more stable. Still, the measurement noise cannot be too large. The final values I settled on was 0.1, which is about $6\circ/s$.

<center><video width="600"><source src="/ECE4960/assets/videos/lab12/measurement.mp4"></video></center>

<center><img src="/ECE4960/assets/images/lab12/measurement.png" width="600"></center> 

The system is still pretty stable even though the estimates are quite noisy.

To modify matrices A and B, I decided to simply scale the matrices upwards until the systems starts breaking again. First, I only scaled A by 30%. The system went absolutely nuts. The cart literally flew out of the universe, and then some.

<center><img src="/ECE4960/assets/images/lab12/a13.png" width="600"></center> 

10% was no better.

<center><img src="/ECE4960/assets/images/lab12/a11.png" width="600"></center> 
<center>10%</center>

<center><img src="/ECE4960/assets/images/lab12/a103.png" width="600"></center> 
<center>3%</center>

<center><img src="/ECE4960/assets/images/lab12/a101.png" width="600"></center> 
<center>1%</center>

<center><img src="/ECE4960/assets/images/lab12/a1001.png" width="600"></center> 
<center>0.1%</center>

<center><img src="/ECE4960/assets/images/lab12/a10001.png" width="600"></center> 
<center>0.01%</center>

<center><img src="/ECE4960/assets/images/lab12/a100001.png" width="600"></center> 
<center>0.001%</center>

Finally, something that doesn't break physics. I then tried to increase scale B in the same way. I learned my lesson and started trying at 0.1%.

<center><img src="/ECE4960/assets/images/lab12/b1001.png" width="600"></center> 
<center>0.1%</center>

<center><img src="/ECE4960/assets/images/lab12/b10001.png" width="600"></center> 
<center>0.01%</center>

B seems to be a lot more forgiving. What if A and B are both changed by the values that work from above?

<center><img src="/ECE4960/assets/images/lab12/ab.png" width="600"></center> 