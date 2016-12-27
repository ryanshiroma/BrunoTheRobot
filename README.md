# Coding a Bayesian Analysis Approach for a two-wheeled robot

<img align="right" src="readmefiles/dog.jpg" width="360">

Balancing robots have always been a fascinating idea to us. Although it is second-nature to us to stand upright, watching a 1 year old baby, or robot do the same feels so magical. The vulnerability of unstable footing seems so humanizing - counter to the traditional notion of a rugged, calculated, robot. And with the recent introduction of low cost sensor electronics, building a robot to emulate this movement is within reach of any electronics hobbyist. 
So let's first stop and think about how we balance on our two feet. When we feel that we are falling forward, we take a step forward, and when we feel we are falling backward, we take a step back. This same idea goes for two wheeled robots; If the robot knows its leaning in one direction, it can correct itself by driving in the direction of that lean. Therefore, the only information needed in order to keep a two-wheeled robot upright is the degree of this lean. This seems simple enough... so let's design a robot to automate this balancing act!

**OK, so how can we measure the lean angle?**

An accelerometer sensor can provide us with an *angle*(lean) measurement. Additionally, a gyroscope sensor can measure an *angular velocity*, giving us even more information on this lean. Ok let's collect some data where the robot is being held in perfect steady balance, not moving and straight upright.

 <p align="center">
<img src="readmefiles/acc1.png" width="400"><img src="readmefiles/gyro1.png" width="400">
</p>
Looking at these graphs you might ask, *Wait, why aren't these sensors reading zero degrees? And why do the measurements jump around?*
 
Unfortunately, robotic sensors aren't perfect; all sensors are subject to bias and noise in their readings to some degree. This can be due to a variety of factors that can't be controlled including stray magnetic fields, ambient temperature, manufacturing tolerances, etc. 

If we want accurate readings of the lean angle, we will need to learn how to deal with these issues.
How did we decide to accomplish this? **Bayesian methods!**
Problem| Solution
--- | ---
 What are these biases and noise levels? | Bayesian Estimation
How do we account for these issues when figuring out lean angle over time? | Recursive Bayesian Updating

Once we can answer these two problems, we can figure out an accurate lean angle, and finally drive the motors to balance the robot. Let's jump into our Bayesian approach solutions.


 <p align="center">
<img src="readmefiles/bruno2.JPG" width="360">
</p>

## Problem 1:  What are these biases and noise levels?
We first made the assumption that the bias is some unknown constant and that the noise is normally distributed. This is a common assumption for sensor data. We can think of the sensor noise as the summation of many small unknown distribution random variables coming from things like magnetic fields, temperature etc. thus invoking the CLT.
Bayesian Sensor Calibration

## Problem 2: How do we account for these issues when figuring out lean angle over time? 
 Recursive Bayesian Updating


## 1. Pre-Processing
In this step, we used existing sensor data collection libraries to query each sensor 100 times per second. The current sensor readings for the rest of the code are named as follows:

Sensor |What does it measure? | Variable Name
--- |---| ---
Accelerometer |number of degrees off of a vertical robot position |  `angle` 
Gyroscope | `angular_vel`


Combine two noisy angle sensor readings into a single prediction for the current angle. 
```C
void setMotorSpeedM2(float vel){
   myTimer2.end();
   if(vel>0){
      digitalWriteFast(Motor2Dir,0);
   }else{
      digitalWriteFast(Motor2Dir,1);
   }
   myTimer2.begin(step_one2, 43478/abs((vel)));  
}


```


Now that we've got an estimate for the current angle of the robot, we can now drive the motors in a way that automatically balances it.  This involves knowledge of control systems and microcontroller hardware interrupts. Code for this part is in the INO file however both concepts are beyond the scope of this writeup but can be researched below:

https://en.wikipedia.org/wiki/PID_controller

https://arduinodiy.wordpress.com/2012/02/28/timer-interrupts/


## Future Work
1. Try doing sensor calibration using a Hierarchical Bayes approach.
We can do this under the following assumptions:
 - Each time we start up the robot and calibrate, the bias comes from one common hyperprior distribution.
 - each time we calibrate, we are ignorant to any differences that might affect bias thus giving us exchangeability. *(we know that ambient temperature and battery voltage does affect biases and noises but we don't have temps and voltages available as known data)*
2. Try to incorporate the uncertainty in our bias estimates in our update step. As of now, this information is not used in the analysis.


