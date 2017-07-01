# MPC Controller

* [The Goal](#the-goal)
* [Rubric](#rubric)
* [Code](#code)
* [Model](#model)
* [Timestep length and Elapsed duration](#timestep-lenght-and-elapsed-duration)
* [Polynomial fitting](#polynomial-fitting)
* [Latency](#latency)
* [Output Video](#output-video)

---

## The Goal

For this project the goal is to implement Model Predictive Control to drive a car around a track within the Simulator. No tire may leave the drivable portion of the track surface. The car may not pop up onto ledges or roll over any surfaces that would otherwise be considered unsafe (if humans were in the vehicle).

## [Rubric](https://review.udacity.com/#!/rubrics/896/view)
Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Code
The project is organized as follows:
* [`src`](https://github.com/vguerra/CarND-MPC-Project/tree/master/src): Folder containing the source files.
    - [`main.cpp`](https://github.com/vguerra/CarND-MPC-Project/blob/master/src/main.cpp): Implements communication with simulator and pass the needed parameters received from the Simulator into the MPC implementation.
    - [`MPC.cpp`](https://github.com/vguerra/CarND-MPC-Project/blob/master/src/MPC.cpp): Implements MPC.

### Model

The state vector includes 6 variables:

* `x`: Vehicle's position on x axis.
* `y`: Vehicle's position on y axis.
* `psi`: Vehicle's heading direction.
* `v`: Vehicles's velocity.
* `cte`: The cross track error.
* `epsi`: The orientation error.

We have as well the actuators, that represent the control inputs:

* `delta`: Vehicle's steering angle.
* `a`: Vehicle's acceleration.

### Timestep length and Elapsed duration

The values chosen are:

* N = 10
* dt = 0.1

It is enough to have 10 steps to look into the future.

### Polynomial fitting

First, the waypoints provided by the Simulator are [translated into the Vehicle's coordinate system](https://github.com/vguerra/CarND-MPC-Project/blob/master/src/main.cpp#L99-L105).

After that, a [3rd order polynomial is fitted](https://github.com/vguerra/CarND-MPC-Project/blob/master/src/main.cpp#L112) to the transformed waypoints. In this case a polynomial with an order bigger than 2 is required to capture the roadh shape, anything less than that will not be of much help.

### Latency

Before proceeding with computing the desired steering angle and throttle value, we take into account the latency. There is a 100 ms. latency between actuations commands on top of the connection latency, therefore we try to [anticipate for the initial state](https://github.com/vguerra/CarND-MPC-Project/blob/master/src/main.cpp#L115-L122) where the vehicle might be in 100 ms. in the future. For that, we apply the kinetic model equations assuming the car starts at position `(0, 0)` and moves with the given velocity `v` and steering angle `steering_angle` that we get from the simulator.

With this data we are able to compute the state of our vehicle in 100 ms.

After that we proceed to compute the [optimized steering angle and throttle](https://github.com/vguerra/CarND-MPC-Project/blob/master/src/main.cpp#L124-L130).

### Output Video