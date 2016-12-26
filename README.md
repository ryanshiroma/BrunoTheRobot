# Coding a Bayesian Analysis for Bruno, the two-wheeled robot
 <p align="center">
<img src="readmefiles/bruno2.JPG" width="360">
</p>

Why is a balancing robot so hard to implement? Balancing is second nature
tpr let's stop and think about how we balance on our two feet. When we feel that we are falling forward, we take a step forward, and when we feel we are falling backward, we take a step back. This same idea goes for two wheeled robots; If the robot knows its leaning in one direction, it can correct itself by driving in the direction of that lean. Therefore, the only information needed in order to keep a two-wheeled robot upright is the degree of this lean. This seems simple enough... so let's design a robot to automate this balancing act!

**OK, so how can we measure the lean angle?**

An accelerometer sensor can provide us with an *angle*(lean) measurement. Additionally, a gyroscope sensor can measure an *angular velocity*, giving us even more information on this lean. Ok let's collect some data where the robot is being held in perfect steady balance, not moving and straight upright.

 <p align="center">
<img src="readmefiles/acc1.png" width="400"><img src="readmefiles/gyro1.png" width="400">
</p>
Looking at these graphs you might ask,

 *Wait, why aren't these sensors reading zero degrees? And why do the measurements jump around?*
 
Unfortunately, robotic sensors aren't perfect; all sensors are subject to bias and noise in their readings to some degree. This can be due to a variety of factors that can't be controlled including stray magnetic fields, ambient temperature, manufacturing tolerances, etc. 
If we want accurate readings of the lean angle, we will need to learn how to deal with these issues.

How can we accomplish this? **Bayesian methods!**
Problem| Solution
--- | ---
 What are these biases and noise levels? | Bayesian Sensor Calibration
How do we account for these issues when figuring out lean angle over time? | Recursive Bayesian Updating


Now let's jump into solving these two problems.

### Bayesian Sensor Calibration

### Recursive Bayesian Updating


## 1. Pre-Processing
In this step, we used existing sensor data collection libraries to query each sensor 100 times per second. The current sensor readings for the rest of the code are named as follows:

Sensor |What does it measure? | Variable Name
--- |---| ---
Accelerometer |number of degrees off of a vertical robot position |  `angle` 
Gyroscope | `angular_vel`
## Bayesian Updating
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



