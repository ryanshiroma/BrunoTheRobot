# Coding a Bayesian Analysis for Bruno the two wheeled robot
 <p align="center">
<img src="readmefiles/bruno2.JPG" width="360">
</p>
A baby usually learns how to balance on two feet at around a year old.

tpr let's stop and think about how we balance on our two feet. When we feel that we are falling forward, we take a step forward, and when we feel we are falling backward, we take a step back. This same idea goes for two wheeled robots; If the robot knows its leaning in one direction, it can correct itself by driving in the direction of that lean. Therefore, the only information needed in order to keep a two wheeled robot upright is the degree of this lean. This seems simple enough... so let's design a robot to automate this balancing!

**OK, so we just need to know the amount of lean. How can we measure this?**
An accelerometer sensor can provide us with an angle(lean) measurement. Additionally,, a gyroscope sensor can measure the change in angle, giving us even more information on this *lean*.

**OK, let's use these lean measurements to drive the motors to balance and voila. Easy as that right?**
Unfortunately no 🙁 robotic sensors aren't perfect; all sensors are subject to bias and noise in their readings to some degree. This can be due to a variety of factors that can't be controlled including stray magnetic fields, ambient temperature, manufacturing tolerances, etc. Because of this, our first step is to get a handle on what the bias and noise is for each sensor.


--- | Step | Description
--- | --- | ---
1 |Pre-Processing | Collect noisy raw data
2 |Bayesian Analysis |Given data, estimate the current angle of the robot
3 | Post-Processing | Given the angle, drive the motors to keep the robot upright

     

The purpose of this readme is to explain the Bayesian analysis step.
## 1. Pre-Processing
In this step, we used existing sensor data collection libraries to query each sensor 100 times per second. The current sensor readings for the rest of the code are named as follows:

Sensor |What does it measure? | Variable Name
--- |---| ---
Accelerometer |number of degrees off of a vertical robot position |  `angle` 
Gyroscope | `angular_vel`
## 2. Bayesian Analysis
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
