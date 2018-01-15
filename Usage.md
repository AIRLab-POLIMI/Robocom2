# Robocom documentation

## Introduction  
The purpose of this document is to describe the steps that are necessary to have Robocom explore an unknown environment. It also contains a variety of issues, TODOs and other stuff that may be useful for future reference.  

## Getting access to Robocom
Login credentials for robocom are:
``` 
username: robocom
password: aerolabio
```  

There are two ways to get access to Robocom.  

The first is to simply connect a mouse and keyboard to the USB ports located on the main PC board and then plug a display to the VGA video port. There seems to be a bug that causes the display to remain turned off if the VGA cable is connected after the PC has already been turned on; to avoid it, remember to first connect the VGA cable and then turn on the PC.  

The second is to login remotely via ssh. Robocom has network access through an onboard Linksys router and is configured to use the `192.168.1.102` IP address, which is also associated with the `airlab` hostname.   
To be able to login remotely, it is necessary to be on the Robocom WiFi network (password: `aerolabio`); ssh is configured to use port 1337.   
It is possible to login by directly specifying Robocom's IP address as well as its hostname; for the sake of this tutorial, we'll call the user machine `marvin`:  
``` 
user@marvin:~/$ ssh robocom@192.168.1.102 -p1337
```
``` 
user@marvin:~/$ ssh robocom@airlab -p1337
```
In this configuration, the IP of `marvin` will be set by the Linksys router to `192.168.1.104`.

## Setting up Robocom  
Robocom is equipped with a compact PC board running Ubuntu 16.04.2 LTS xenial and ROS kinetic 1.12.7, which has the task of running the necessary packages to communicate with the NovaCore boards connected to the motors and the sensors. The relevant packages, which are located inside the src folder of the catkin workspace at `/home/robocom/catkin_ws`, are the following:  

* Robocom2/robocom: provides the Odometry node and the launch file that coordinates the startup of the system.  
* Robocom2/robocom_msgs: provides the Proximity msg that is internally used by NovaCore to broadcast the sonar information (currently unused, as sonars are not mounted).  
* ros-rosserial_core: provides the routines to handle the communication with the NovaCore boards; this can be directly downloaded and updated from its git repository via ```git clone https://github.com/novalabs/ros-rosserial_core.git```.  

These packages should already be built and work fine. In case of updates to their source code, it is necessary to rebuild the workspace:  
``` 
robocom@airlab:~/catkin_ws$ catkin_make
robocom@airlab:~/catkin_ws$ rospack profile
```
It is also a good idea to make sure that the following packages are installed:
```
robocom@airlab:~$ sudo apt install ros-kinetic-rosserial ros-kinetic-joy ros-kinetic-navigation ros-kinetic-teleop-twist-joy ros-kinetic-lms1xx ros-kinetic-freenect-camera
```

## Start Robocom
The minimal setup for Robocom is provided by the `robocom_minimal.launch` file inside the `Robocom2/robocom` package. It can be launched with the syntax:  
```robocom@airlab:~$ roslaunch robocom robocom_minimal.launch bag:=/path/to/bagfile_to_record.bag```  
This launch file:  

* Starts `rosserial_core` to enable communication with the NovaCore boards;
* Spawns the `joy` node and the `teleop` node to allow manual movement of the robot via the Joypad;  
* Launches the odometry, laser and kinect nodes; 
* Provides the laser-baselink transform (to be fixed).
* Records the /odom and /scan topics in the bag file specified with the `bag` launch argument. 

The laser is configured to be accessible at ```192.168.1.4```.

## Setup remote control
As one of the prerequisites of an autonomous mobile robot is the ability to roam the environment autonomously, Robocom is equipped with an on-board PC that runs a full version of ROS and that could theoretically be used to launch whatever package is required by the specific application. However, as that PC is rather underpowered, in most applications it's actually preferrable to offload the most computationally intensive tasks to an external computer. To support this scenario, Robocom is equipped with an on-board wireless router that provides WiFi connectivity via the ```robocom``` SSID. The router is also internally connected to the on-board PC and the sensors.  
By design, ROS is perfectly capable of running on multiple machines at the same time in a completely transparent fashion. To configure a multiple machine setup, it is necessary to take the following steps.

### 1. Start the master
There must be one and only one machine running the master, which can either be Robocom itself or an external computer. Please note that ROS expects to address machines by their hostnames. Robocom's hostname is `airlab`; for the sake of this tutorial, we'll call the other machine `marvin`.  
The first step is to start the master, which in this case is run on `marvin`:
```
user@marvin:~$ roscore
```  
Depending on what kind of access is available to the different computers across which ROS is run, it may be necessary to remotely login into them via `ssh` to execute these commands.  

### 2. Set the master URI
In order to identify which machine is running the master, ROS packages lookup the `ROS_MASTER_URI` environment variable of the shell in which they are launched. The `ROS_MASTER_URI` property must be set on all computers that are cooperating to govern Robocom, in this case `airlab` and `marvin`.  
To set the `ROS_MASTER_URI`:  
```
user@marvin:~$ export ROS_MASTER_URI=http://marvin:11311
robocom@airlab:~$ export ROS_MASTER_URI=http://marvin:11311
```

### 3. Launch the packages  
It is now sufficient to start the desired launch files on each machine.  
On Robocom's on-board PC `airlab`, it is necessary to launch the `robocom_minimal.launch` file:
```
robocom@airlab:~$ roslaunch robocom robocom_minimal.launch bag:=/path/to/bagfile_to_record.bag
```
On the other machines, launch the nodes that are relevant to the specific application. As an example, we'll launch the automatic exploration launch file from the `slampbenchmarking` project:  
```
user@marvin:~$ roslaunch exploreambient_robocom.launch outputfile:=/path/to/robocomOut.log log_path:=/path/to/logfiles
```
where `outputfile` is where the gmapping SLAM trajectory estimate is saved and `log_path` is where the `explorer` package saves its logs.

## Frames, topics etc.  
* Odometry is published on the `/odom` topic and broadcasted through the `/odom` to `/base_link` transform at a 100Hz; its frame is `odom`.  
* The SICK laser scans are published on the `/scan` topic; its frame is `laser_base`.  

## Known issues  
* There is an issue that arises when Robocom is powered by an external power supply and a step signal with `x > 0.4` is manually sent using `rosserial_core`. In this case, the robot either shuts down or reboots. The likely cause is some sort of current peak, which may be related with limitations of the power supply or with the usage of capacitors to decouple the motors controller board from the rest of the circuitry. The issue does not arise if the motors are powered by a separate 24V circuit or if the entire robot is powered by batteries.  
* There is an issue with Joy in which the robot may experience sudden and unexpected losses of signal when controlled via joypad, in which case the last sent command is repeated for an unspecified amount of time always with the same timestamp. This issue seems to be specifically related to the Joy node; other control mechanisms, such as direct drive of the `/cmd_vel` topic via `rqt` or `move_base`, are not affected.  






