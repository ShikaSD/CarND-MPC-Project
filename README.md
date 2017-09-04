# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

---

## Model

The state includes: position `(x, y)`, velocity `(v)`, acceleration `(a)`, vehicle angle relative to map X axis `(psi)`, vehicle steering angle `(delta)`, cross-track error `(cte)` and angle of the vehicle relative to track direction `(epsi)`.

The actual state for the predicted steps is depends on reference state and the actuators applied. Actuators(acceleration, steering) adjustment allows to improve the model cost and result in more optimized trajectory. The implementation based on the formulas from the lesson can be seen in the lines 52-93.

To improve the actuators influence on the car, the cost should be defined properly. The most important factor is the **cte** which ensures that car is following the track. Nevertheless, the **epsi** metric is important as well to ensure that right delta is applied and the car direction does not vary a lot. The other cost parts, as actuators themselves or actuators delta are added to remove unnecessary fluctuations. To ensure movement forward, the **velocity** difference to target velocity is added as well.

## Timesteps

To choose right values for the time step calibration (`N` (amount of steps) and `dt` (time delta between each step)) two things should be considered: precision and performance. Smaller `N` improves performance, whereas smaller `dt` improves precision. However, among this factors, the distance of the predicted track should be considered as well. If the distance is too small, the MPC will perform poorly due to inability to look far enough to the "future".
First values were chosen to be 20 time steps of 0.1 second each. However, the performance was poor, so the amount of the timesteps was decreased to **10** and time delta increased to **0.15**.

## Preprocessing

Before fitting polynomial and passing values to the MPC, the x and y values are translated to fit car coordinate system, which gives easier resolution for the further calculations. First, the coordinates were translated by `(-x, -y)` and then rotated by `psi`.

## Latency problem

The problem with latency is overcome by shifting initial inputs to the "future" by 100ms. Then the MPC predicts the values the same way from the next state as it was doing before.
