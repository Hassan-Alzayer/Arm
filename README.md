Plans and motions for a robot arm can be planned and carried out using ROS packages, both in simulation and in real life.These packages have been tested with Ubuntu 20 and ROS noetic.The Moveit plugin is used by the robot arm to apply kinematics.
Dependencies
run this instruction inside your workspace:

  rosdep install --from-paths src --ignore-src -r -y

make sure you installed all these packages:

for kinetic:

$ sudo apt-get install ros-kinetic-moveit
$ sudo apt-get install ros-kinetic-joint-state-publisher ros-kinetic-joint-state-publisher-gui
$ sudo apt-get install ros-kinetic-gazebo-ros-control joint-state-publisher
$ sudo apt-get install ros-kinetic-ros-controllers ros-kinetic-ros-control

for melodic:

$ sudo apt-get install ros-melodic-moveit
$ sudo apt-get install ros-melodic-joint-state-publisher ros-melodic-joint-state-publisher-gui
$ sudo apt-get install ros-melodic-gazebo-ros-control joint-state-publisher
$ sudo apt-get install ros-melodic-ros-controllers ros-melodic-ros-control

for noetic:

$ sudo apt-get install ros-noetic-moveit
$ sudo apt-get install ros-noetic-joint-state-publisher ros-noetic-joint-state-publisher-gui
$ sudo apt-get install ros-noetic-gazebo-ros-control joint-state-publisher
$ sudo apt-get install ros-noetic-ros-controllers ros-noetic-ros-control
Steps:
1- Insert new folder into your home name = "catkin_ws'



2- inside "catkin_ws" folder insert new folder name = "src"



3- Add the "aurduino_robot_arm" package to "src" folder

$ cd ~/catkin_ws/src

$ sudo apt install git

$ git clone https://github.com/Hussein-Almansory/Arm-Package-on-ROS.git

4- After you add it into "src" folder you will see these contents:



Robot Arm
The robot arm has 5 joints only 4 joints can be fully controlled via ROS and Rviz, the last joint (gripper) has a default motion executed from the Arduino code directly.

Robot initial positions
pos

Setting up Arduino using ROS
Installing Arduino IDE in Ubuntu is as simple as unzipping the files and running:
  $ sudo./install.sh.
Launch the Arduino IDE
Install the arduino package and ros library
http://wiki.ros.org/rosserial_arduino/Tutorials/Arduino%20IDE%20Setup
Make sure to change the port permission before uploading the Arduino code
  $ sudo chmod 777 /dev/ttyUSB0
Usage
Controlling the robot arm by joint_state_publisher
  $ roslaunch robot_arm_pkg check_motors.launch

if you face this problem same this picture:

probeln

To solve this problem follow these steps:
1) Write this code into your shell:


2) Insert this code same the picture at the bottom of the "barshrc.sh" file

NOTE : source the catkin_ws folder [ SELECT THE CORRECT PATH WHERE YOU ADD IT]
3) The problem solved and the program run without errors:


Simulation
Run the following instructions to use gazebo

$ roslaunch robot_arm_pkg check_motors.launch
$ roslaunch robot_arm_pkg check_motors_gazebo.launch
$ rosrun robot_arm_pkg joint_states_to_gazebo.py
(You may need to change the permission)

   sudo chmod +x ~/catkin_ws/src/arduino_robot_arm/robot_arm_pkg/scripts/joint_states_to_gazebo.py

Controlling the robot arm by Moveit and kinematics
  $ roslaunch moveit_pkg demo.launch

You can also connect with hardware by running:

  $ rosrun rosserial_python serial_node.py _port:=/dev/ttyUSB0 _baud:=115200

(Note: You may need to use ttyACM)

Simulation
Run the following instruction to use gazebo

$ roslaunch moveit_pkg demo_gazebo.launch

Pick and place by using OpenCV
Preparation
Download webcam extension for VirtualBox

https://scribles.net/using-webcam-in-virtualbox-guest-os-on-windows-host/
Testing the camera and OpenCV
Run color_thresholding.py to test the camera

Before running, find the camera index normally it is video0

$ ls -l /dev | grep video

If it is not, update line 8 in color_thresholding.py

8 cap=cv2.VideoCapture(0)

Then run

$ python color_thresholding.py
Using OpenCV with the robot arm in ROS
In Real Robot
In a terminal run
$ roslaunch moveit_pkg demo.launch

this will run Rviz

connect with Arduino:

select the Arduino port to be used on Ubuntu system

change the permissions (it might be ttyACM)

$ ls -l /dev | grep ttyUSB

$ sudo chmod -R 777 /dev/ttyUSB0

upload the code from Arduino IDE

$ rosrun rosserial_python serial_node.py _port:=/dev/ttyACM0 _baud:=115200

In another terminal
$ rosrun moveit_pkg get_pose_openCV.py

This will detect blue color and publish the x,y coordinates to /direction topic

(Note: check the camera index and update the script if needed)

Open another terminal
$ rosrun moveit_pkg move_group_node

This will subscribe to /direction topic and execute motion by using Moveit move group

The pick and place actions are performed from the Arduino sketch directly.

In simulation (Gazebo)
In a terminal run
$ roslaunch moveit_pkg demo_gazebo.launch

this will run Rviz and gazebo

In another terminal
$ rosrun moveit_pkg get_pose_openCV.py

This will detect blue color and publish the x,y coordinates to /direction topic

(Note: check the camera index and update the script if needed)

Open another terminal
$ rosrun moveit_pkg move_group_node

This will subscribe to /direction topic and execute motion by using Moveit move group

We can’t visualize the pick and place actions in gazebo
