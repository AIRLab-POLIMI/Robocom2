# Odometry measures

## 1. Wheel angular velocity
The following tests have been performed by sticking a marker made of tape to one of the wheels and measuring the amount of time taken to perform a fixed number of turns.  

Test 1:   

* Setpoint: x = 0.1 m/s  
* Time: 82.5 seconds  
* Number of turns: 10   
* Estimated angular velocity: 10 * 2pi / 82.5 = 0.76159  
* Sample measures from /enc = x: 0.755680441856, x: 0.759925842285, x: 0.764171242714  

Test 2:  

* Setpoint: x = 0.2 m/s  
* Time: 82.8 seconds
* Number of turns: 20 
* Estimated angular velocity: 20 * 2pi / 82.8 = 1.51767  
* Sample measures from /enc = x: 1.51136088371, x: 1.51560628414, x: 1.50711548328  

Test 3:  

* Setpoint: x = 0.1 m/s  
* Time: 165.0 seconds  
* Number of turns: 20  
* Estimated angular velocity: 20 * 2pi / 165.0 = 0.76159  
* Sample measures from /enc = x: 0.75134981823, x: 0.759925842285, x: 0.755680441856    

Test 4:  

* Setpoint: x = 0.2 m/s  
* Time: 288.8 seconds  
* Number of turns: 70   
* Estimated angular velocity: 70 * 2pi / 288.8 = 1.52293  
* Sample measures from /enc = x: 1.51560628414, x: 0.150286996365, x: 1.51985168457  

## 2. Linear velocity  
The following tests have been performed by positioning Robocom at the edge of an horizontal line of white tape, orthogonally to the line itself, and having it drive until a second horizontal line of white tape. The distance between the two lines was measured with a meter at 4.366 m.  

Test 1:    

* Setpoint: x = 0.05 m/s  
* Distance: 4.366 m  
* Estimated distance from /odom: 4.415 m   
* Time: 93.5 seconds  
* Estimated velocity: 0.0466 m/s  
* Sample measures from /vel: x 0.0491168610752 m/s, x 0.0485555268824 m/s, x 0.0491168610752 m/s  

Test 2:   

* Setpoint: x = 0.1 m/s  
* Distance: 4.366 m  
* Estimated distance from /odom: 4.424 m  
* Time: 46.8 seconds  
* Estimated velocity: 0.0932 m/s    
* Sample measures from /vel: x = 0.100198403001 m/s, x = 0.100759744644 m/s, x = 0.0996370613575 m/s, x =  0.0990757197142 m/s    


Test 3:  

* Setpoint: x = 0.1 m/s  
* Distance: 4.366 m  
* Estimated distance: 4.361 m  
* Time: 47.2 seconds  
* Estimated velocity: 0.0925 m/s  
* Sample measures from /vel: x = 0.0990757197142 m/s, x = 0.0993563979864 m/s,  x = 0.100198403001 m/s  

## 3. Angular velocity  
The following tests have been performed by positioning Robocom in the middle of the AIRLab laboratory, having it spin on itself at a given setpoint velocity and measuring the time required to perform a fixed number of full turns.  

Test 1:  

* Setpoint: z = 0.5 rad/s  
* Number of turns: 11  
* Time: 150.7 seconds  
* Estimated angular velocity: 11 * 2pi / 150.7 = 0.4586 rad/s  
* Sample measures from /vel : z = 0.501148641109 rad/s, z = 0.491322189569 rad/s, z = 0.493778795004 rad/s  

Test 2:   

* Setpoint: z = 0.5 rad/s  
* Number of turns: 10  
* Time: 136.3  
* Estimated angular velocity:  10 * 2pi / 236.3 = 0.4599 rad/s  
* Sample measures from /vel: z = 0.501148581505, z = 0.497463703156,  z = 0.495007097721  