# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

---

## Model

I used a kinematic model with two actuators - steering and throttle/brakes.  
Steering consists of how much the steering wheel is turned. The throttle and brakes are combined into a single actuator where any positive value signifies forward motion where any negative motion signifies brakes are applied.  
Although this model fails to take into account many real world parameters like the vehicle/passenger mass, tire pressure, slip angles, etc. it is still a good approximation for this exercise.  
  
To predict the vehicle's state at the next timestamp, we use the following equations:  
```
x1 = x0 + v * cos(psi) * dt  
y1 = y0 + v * sin(psi) * dt
v1 = v * a * dt
psi1 = psi0 + v / Lf * s * dt  (Lf = distance between front of vehicle and its center of gravigy, s = steering angle)

```

## Timestep Length and Elapsed Duration (N and dt)
I chose to use N = 8 and dt = 0.1. I experimented with a couple different options (N = 10, 15; dt = 0.2, 0.15) but I found this to be the best combination. I wanted to make sure I'm not predicting too far into the future to allow the car to adapt to changing conditions (like sudden curves). I also wanted to make sure I wasn't doing too many calculations with each timestep to make sure the car could effectively update its controls in real time.  

## Polynomial Fitting and MPC Preprocessing
Before fitting a line to all the waypoints, I transformed them into the vehicle's coordinate system from the map coordinate system. This had to be done because all controls are given in relation to the vehicle's current positon and heading, not in relation to the map's origin.  

I then fit these waypoints to a 3rd degree polynomial which will be used to calculate the car's cross-track and orientation errors.

## Model Predictive Control with Latency
To account for latency after issuing the controls to the vehicle, I ensured all my calculations were done as if the vehicle was located one timestep in the future. As soon as I receive a message from the vehicle, I transform the vehicle's x and y coordinates to the next timestep using the same kinematic model that governs the MPC. After the x & y coordinates are transformed, the waypoints are adjusted to this point, which will be used for the rest of the MPC process.



