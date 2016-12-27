# Coding a Bayesian Analysis Approach for a two-wheeled robot

<img align="right" src="readmefiles/dog.jpg" width="400">

Balancing robots have always been a fascinating idea of mine. Although it's second-nature to us to stand upright, watching a 1 year old baby or robot do the same feels so magical. The vulnerability of unstable footing seems so humanizing - counter to the traditional notion of a rugged, calculated, robot. And with the recent introduction of low cost sensor electronics, building a robot to emulate this movement is within reach of any electronics hobbyist. 

So let's first stop and think about how we balance on our two feet. When we feel that we are falling forward, we take a step forward, and when we feel we are falling backward, we take a step back. This same idea goes for two wheeled robots; If the robot knows its leaning in one direction, it can correct itself by driving in the direction of that lean. Therefore, the only information needed in order to keep a two-wheeled robot upright is the degree of this lean. This seems simple enough... so let's design a robot to automate this balancing act!

**OK, so how can we measure this lean angle?**

An accelerometer sensor can provide us with an *angle*(lean) measurement. Additionally, a gyroscope sensor can measure an *angular velocity*, giving us even more information on this lean. Ok let's collect some data where the robot is being held in perfect steady balance, not moving and straight upright.

 <p align="center">
<img src="readmefiles/acc1.png" width="400"><img src="readmefiles/gyro1.png" width="400">
</p>
Looking at these graphs you might ask, 
*Wait, why aren't these sensors reading zero degrees? And why do the measurements jump around?*
 
Unfortunately, robotic sensors aren't perfect; all sensors are subject to bias and noise in their readings to some degree. This can be due to a variety of factors that can't be controlled including stray magnetic fields, ambient temperature, manufacturing tolerances, etc. 

If we want accurate readings of the lean angle, we will need to learn how to deal with these issues.
How did we decide to accomplish this? **Bayesian methods!**

Problem | Solution
--- | ---
Identify Bias and Noise in the Sensors | Bayesian Estimation
Make Predictions for the Current Angle | Recursive Bayesian Updating

Once we can answer these two problems, we can figure out an accurate lean angle, and finally drive the motors to balance the robot.


 <p align="center">
<img src="readmefiles/bruno2.JPG" width="360">
</p>
 Let's jump into our Bayesian approach solutions.
## Problem 1:  Identify Bias and Noise in the Sensors
In order to trust the sensor readings we will need to first figure out how the reading relates to the true lean angle. So the first step we need to do is to figure out how the sensor data is distributed when the sensors are being held at a known angle (0 degree lean). This is like doing a calibration of the sensors every time the the robot is turned on.
#### How is the sensor data distrubuted?
We assume is that the data is normally distributed, centered at some bias amount <img src="http://mathurl.com/ygnyf6e.png">, and noise amount <img src="http://mathurl.com/abetvkx.png">. We can think of the sensor noise as the summation of many small unknown distribution random variables coming from things like stray magnetic fields, temperature etc., thus invoking the central limit theorem and consequently, a normal distribution.
 <img src="http://mathurl.com/zo2c6mj.png">
 where y is the observed sensor reading.
#### What are the parameters for this normal distribution?
Now that we know that the sensor data is normal, we must determine the distribution parameters of each sensor. But WAIT! We don't know what the noise level is OR the amount of bias... Both are unknown to us...
From here we take on a Bayesian approach to estimating these parameters.
### Bayesian Estimation of Sensor Bias and Noise
#### Prior Distribution Formulation
While we don't know what the bias and noise values truly are, we do however have some information from the manufacturer on what these values should *approximately* be.

###### Accelerometer Datasheet Information

  <img src="readmefiles/noiseacc.png">

###### Gyroscope Datasheet Information

 <img src="readmefiles/noisegyro.png">

 <img src="readmefiles/arrowdown.jpg" width="150">

Sensor | Manufacturer's Bias Estimate | Manufacturer'as Noise Estimate
--- | --- | ---
Accelerometer | 0 | 0.030 degrees^2
Gyroscope | 0 | 0.048 dps^2


We'll now use this information to form our own belief of the bias and noise parameters:

 
Sensor | Our Bias Belief | Our Noise Belief
--- | --- | ---
Accelerometer | 95% between -3 and 3 | 95%  between 0 and 0.05
Gyroscope |  95% between -2 and 2  |  95%  between 0 and 0.08



By using a conjugate prior distribution for an unknown mean and unknown variance normal sampling distribution we arrive at a Normal-Inverse-Chi-Squared distribution.
We then can compute parameters for an informative prior distribution for each sensor by incorporating our beliefs.
 <p align="center">
<img src="http://mathurl.com/jupngvn.png">
<img src="http://mathurl.com/hmux9k5.png">
</p>

 <p align="center">
<img src="readmefiles/prioracc.png" width="400"><img src="readmefiles/priorgyro.png" width="400">
</p>
Now that we have our prior distributions set, we can now go ahead and collect some data.
### Data Collection
The way we do this on the robot is by quickly collecting 50 readings within the first second of turning the robot on. The robot needs to be carefully held at an angle we suspect will keep the robot balanced.
### Estimates from the Posterior Distribution
## Problem 2: Make Predictions for the Current Angle 
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

<a href="http://www.youtube.com/watch?feature=player_embedded&v=K-8RJ1lW92k
" target="_blank"><img src="http://img.youtube.com/vi/K-8RJ1lW92k/0.jpg" 
alt="Bruno Video" width="240" height="180" border="10" /></a>


Now that we've got an estimate for the current angle of the robot, we can now drive the motors in a way that automatically balances it.  This involves knowledge of control systems and microcontroller hardware interrupts. Code for this part is in the INO file however both concepts are beyond the scope of this writeup but can be researched below:

https://en.wikipedia.org/wiki/PID_controller

https://arduinodiy.wordpress.com/2012/02/28/timer-interrupts/


## Future Work
1. Try doing sensor calibration using a Hierarchical Bayes approach.
We can do this under the following assumptions:
 - Each time we start up the robot and calibrate, the bias comes from one common hyperprior distribution.
 - each time we calibrate, we are ignorant to any differences that might affect bias thus giving us exchangeability. *(we know that ambient temperature and battery voltage does affect biases and noises but we don't have temps and voltages available as known data)*
2. Try to incorporate the uncertainty in our bias estimates in our update step. As of now, this information is not used in the analysis.


