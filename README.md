# Python_MPC
Thia project consists of a python implementation of a Model Predictive Control (MPC)
 to control the steering angle and the throttle acceleration of my robotic car.
 
## 1.Files
These are the files that are the core part of the project:
* main.py
* mpc.py
* pwm.py

### 1.1 Dependency
This project requires the following dependencies to run:
* matpplotlib
* Python convex optimization library cvxpy.
* numpy

### 1.2 Cvxpy

Install the cvxpy python library using pip or conda ( ``pip install cvxpy or conda install -c cvxgrp cvxpy libgcc``)

## 2. Modle Predictive Control
The [MPC](https://en.wikipedia.org/wiki/Model_predictive_control) is a control technique for complex control problems.Here we are using MPC to optimise the set of actautor inputs that is steering and the throttle values by minimising the cost functions based on the dynamic model of the car.

### 2.1 Prediction Horizon

The prediction horizon is the duration over which the future precdictions are made. The N predictin horizon means that there are N timesteps over which we have to optimise and dt is supposed to be the time elapsed between each time horizon.

The number N also determines the different states of the optimization probelem.Therefore ,higher the value of N,higher is the computational cost of the problem.Low value of N approximates the trajectory poorly and subsequebtly higher values of dt leads to higher cost due to dull representation of the reference trajectory.

Threfore,these have to be tuned manually.I have choosen the values thorugh empirical approach of trial and error.These depend upon the working space of the project ,the latency issues and the the speed of the algorithm.To be put together on the hardware,latency issues must be handled well.

### 2.2 State

The state consists of the model variables of the car .The state is represented by five state variables ``[x,y,psi,v,cte,epsi]``.The ``x`` and ``y`` represent the current location of the car in the global frame ,``psi`` represents the heading of the car.``cte`` represnts the cross track error or how far is the car from the reference trajectory.``epsi`` is the heading error of the car.
The other variables that are used are `` [delta,beta]`` where delta is the steering value and beta is the throotle value.These are the actuator inputs to be minimised.

### 2.3 Model (Update equations)

I have incorported the bicycle model as the dynamic model of the car since it is easier to understand and linearise as well.The cvxpy library expects the state variables to be linear.The following are the update equations for the car:
 
![equation](http://latex.codecogs.com/gif.latex?x_%28t&plus;1%29%20%3D%20x_t%20&plus;%20v_t%20*%20cos%28%5Cpsi_t%29*dt)
![equation](http://latex.codecogs.com/gif.latex?y_%28t&plus;1%29%20%3D%20y_t%20&plus;%20v_t%20*%20sin%28%5Cpsi_t%29*dt)

![equation](http://latex.codecogs.com/gif.latex?%5Cpsi%20_%28t&plus;1%29%20%3D%20%5Cpsi%20_t%20&plus;%20%5Cfrac%7Bv_t%7D%7BL_f%7D*%20%5Cdelta_t%20*%20dt)

![equation](http://latex.codecogs.com/gif.latex?v_%28t&plus;1%29%20%3D%20v%20_t%20&plus;%20a_t%20*%20dt)

![equation](http://latex.codecogs.com/gif.latex?cte_%28t&plus;1%29%20%3D%20f%28x_t%29%20-%20y_t%20&plus;%20v%20_t%20*%20sin%28e%5Cpsi%20_t%29%20*%20dt)

![equation](http://latex.codecogs.com/gif.latex?e%5Cpsi%20_%28t&plus;1%29%20%3D%20%5Cpsi%20_t%20-%20%5Cpsi%20dest%20&plus;%20%5Cfrac%7Bv_f%7D%7BL_f%7D%20*%20%5Cdelta_t%20*%20dt)


``Lf`` measures the distance between the front of the vehicle an### 4.6 Latency

In order to deal with the latency, we have to predict the next state before calling the MPC solver. It can be acoomplished using the Model equations. Below, the pseudocode to predict the next state. 


### 2.4 Latency

Before calling the MPC solver,the car is driven for the time that contributes to the latency i.e the time it takes actuator inputs to act phusically.Here dt is the latency caused by the system. 
```c
dt = 0.1;
x1    = v * cos(0) * dt;
y1    = v * sin(0) * dt;
psi1  = - v/Lf * steer_value * dt;
v1    = throttle_value * dt;
cte1  =   v * sin(epsi1) * dt;
epsi1 = - v * steer_value / Lf * dt;	
```



