# Bruno The Two Wheel Balancing Robot
 
 ![alt text](https://github.com/ryanshiroma/BrunoTheRobot/readmefiles/bruno.png "Logo Title Text 1")
 
Two wheel balancing robot
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
