# Bruno The Two Wheel Balancing Robot
 <p align="center">
<img src="readmefiles/bruno2.jpg" width="360">
</p>
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
