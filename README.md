# Bruno The Two Wheel Balancing Robot Code
 <p align="center">
<img src="readmefiles/bruno2.JPG" width="360">
</p>
#### **The full code can be broken up into four parts:**

--- | Step | How?
--- | --- | ---
1 |Raw Data Collection | Manufacturers' libraries
*2* |*Raw Data -> Angle Estimation*|*my Bayesian analysis code*
3 |Angle Estimates -> Motor Speed |PID controller library
4 | Motor Speed -> Raw Motor Output |Stepper Controller library

The purpose of this readme is to explain the second step of the code, the Bayesian analysis step.
## 1. Raw Data Collection
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
