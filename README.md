# Project 10: MPC
[//]: # (Image References)

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


Overview
---
Implement Model Predictive Control to drive the car around a simulated 

#### The goals / steps of this project are the following:
* Fit 3rd order polynomial on road waypoints
* Define and set MPC calculations and constraints
* Build a Model Predictive Control that handles a 100 millisecond latency
* Tune parameters for a smooth drive around the simulator track

Project Deliverables
---
* `main.cpp` for calling the model and communicating with the simulator
* `MPC.cpp` defines the MPC class with code for MPC implementation and steering&throttle outputs

Discussions
---

**The model**

To fit a polynomial curve to the provided waypoints, the model receives the following from the simulator:
* `ptsx` x-coordinate of the waypoints ahead
* `ptsy` y-coordinate of the waypoints ahead
* `px` coordinate of vehicle's current position 
* `py` y-coordinate of vehicle's current position
* `psi` current orientation angle of vehicle
* `v` current velocity of vehicle
* `delta` current steering angle of the vehicle
* `a` current throttle

Based on the above inputs, the waypoints coordinates are first converted to vehicle coordinates with the following equations:
* ptsx_veh = x * cos(-psi) - y * sin(-psi)
* ptsy_veh = x * sin(-psi) + y * cos(-psi)

Varying weights were used for the following cost variables:
* `cte_w = 1000` cte cost
* `epsi_w = 1000` epsi cost
* `v_w = 1` velocity cost
* `delta_w = 5` steering cost
* `a_w = 5 ` acceleration cost
* `delta_change_w = 50` steering rate of change cost
* `a_change_w = 5` acceleartion rate of change cost

Tuning the above parameters helped improve the driving behavior. Adjusting `cte_w` and `epsi_w` values helped the vehicle stay on the center of the road. One important parameter is `v_w`, which is needed for the car to constantly move. 

With the help of the IPOPT and CPPAD packages, a best fit curve is calculated for optimal trajectory. Note that certain constraints were set before fitting the curve so that the vehicle does not make drastic turns(angle between -25 degrees and 25 degrees) or sudden acceleartions (throttle between -1 and 1).

**Latency**
A latency of 100ms was added for realistic driving condiations. Latency was accounted in the model by predicting where the vehicle would be after 100ms and predict the new state. This allows the vehicle to take action beforehand.

**Tuning timesteps and duration**
I started with 20 timesteps(N) and 0.2s duration(dt) as too large of a prediction horizon(T) would have a hard time coping with the environment changes. I soon found out the N and dt values I started out was too generous as reducing N made the model faster and reducing dT made the model react faster to chaning environments without loss in performance. With a N of 10 and dt of 01.s, the vehicle was able to drive around the track smoothly in quite high velocity.


Results
---

View the **[video](https://youtu.be/khj4jZlyfPI)** on Youtube

---

## Dependencies

* Udacity Simulator [download](https://github.com/udacity/self-driving-car-sim/releases)
* uWebSocket [download](https://github.com/uWebSockets/uWebSockets)
* cmake >= 3.5
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
