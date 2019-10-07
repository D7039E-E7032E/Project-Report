# Collaborative mapping of unknown environments

## Introduction 
<p>
Unknown subterranial areas holds a certain risk for humans to discover and survey. Utilizing modern day technology within robotics, one can minimize these risks.<br>
This project is constructed around this problem, and the goal is to develop software for existing robots to collaborate and map unknown areas such as tunnels and caves. Working on this project is a team of seven students from the Luleå University of Technology. Each student with a background in Computer Science, Electrical Engineering and Control theory.
</p>

## Method

### The Turtlebot
<p>
A Turtlebot 3 Burger is going to be used to solve the given problem, this is mainly because it was provided by the Luleå University of Technology but also due to it's size and components that follows with it.
</p>
<p>
On the top of it there's a 360 Laser Distance Sensor LDS-01 LIDAR, that is going to be used for gathering coordinate points of the surroundings. The LIDAR is connected to a Raspberry Pi 3 Model B, which is going to do all the computing on the Turtlebot. This includes packaging, sending the gathered data to a central station and make sure that the Turtlebot is not exploring an area that it or another Turtlebot has already explored. The Raspberry Pi should also create a personal map with the points that the Turtlebot has gathered. The Raspberry Pi is connected to a OpenCR1.0 board that is going to control the motors and actuators of the wheels.
</p>

### The controller
<p>
The software that will visualise what the robot experiences is RViz. This software will also be chosen because of its tight integration with the ROS framework.
A Turtlebot 3 Burger will be simulated using the simulation software Gazebo. The reason this software will be chosen is because it is tightly integrated with the ROS framework.
</p>

<p>
The software Rviz will make use of a file format called Universal Robotic Description Format (URDF). A URDF file describing the Turtlebot 3 Burger will be used to describe the robot and its capabilities to Rviz. A camera will be added to the robot and therefore a camera will have to be added to the URDF describing the robot. URDF files will be written in XML but by using the macro language xacro. This will make the URDF files easier to maintain and to read. URDF can be visiualised using the URDF package that exists within ROS. This could prove useful to ensure the camera is connected to the body of the robot.
</p>

<p>
The software Gazebo will make use of a file format called Simulation Description Format (SDF). SDF was developed to solve the shortcomings of URDF. SDF specifies such as friction, lights, heightmaps and more. Another improvement is that SDF does not break proper formatting with heavy use of XML attributes and is thus more flexible in its implementation. 
It is also possible to convert URDF into SDF. This lets an URDF file to be used with Gazebo after it has been converted to SDF.
</p>

<p>
ROS makes uses of topics to send messages from a publisher to a subscriber. ROS will publish visualisation messages which RViz will subscribe to and simulation messages will be subscribed to by Gazebo. Gazebo will publish simulation messages which ROS will subscribe to and then publish. Rviz will subscribe to topics that are specified within RViz, such as a camera. A camera will be added to the Turtlebot 3 Burger URDF file. This will cause the ROS to publish a camera feed topic which RViz will subscribe to, allowing for camera feed visualisation. This already works with the LIDAR. 
</p>

<p>
The aim of the simulation is to be able to publish a service call containing a message of a 2D coordinate point and have the simulated Turtlebot 3 Burger navigate towards this 2D coordinate point using the move_base package that is provided within ROS. The 2D coordinate point sent by service call will hereby be referred to as a waypoint. The package move_base provides a vast range of solutions for navigating a robot to a given goal point. This package will be used to define the waypoint navigation for the Turtlebot 3 Burger. It provides strategies for planning navigation and seeing the ongoing execution of the plan.
</p>

### Data gathering
<p>
The LIDAR will gather all the data used to map the environment. Since the LIDAR is not fully accurate a filter of some sort must be implemented to check which points are useful. After the raw data points have been filtered they will be packaged and sent to the central station. The packages will also be cached in the Raspberry Pi for verification that the central station has received the packages. After the packages have been verified they will be removed from the Raspberry Pi.
</p>

### Cartographer
<p>
Cartographer is an algorithm/system used to get a representation of the local environment given useful sensor data. For localization and mapping of the local environment, SLAM is the main part. 

Cartographer contains two SLAMs, one local SLAM and one global SLAM. The local SLAM is to generate approved submaps with correct scans(range data) and the global is reconstruct everything together and creating a global map. Global map can then be used to visually represent the environment.
</p>

### Central station
<p>
In this case Central station is the ROS master. Its role is to look over the other active nodes and help them locate each other. ROS master also comes with a parameter server which will store and retrive parameters from nodes. This parameters are XMLRPC data types which is XML equivalents of common data types.

  
</p>

## Results
<p>
</p>

## Discussion
<p>
</p>

# Appendices

## Gantt Schedule
[Gantt Schedule](ProjectPlan.gan)

### Workflow and Git
<p>
When researching, the main points of the read article should be written down in the WIKI, this is in case someone else has to read up on the same thing. This means that they will not have to read the whole article to learn the main points, also if any important commands are included, they will be written down with a description of what they do.
  
In Git there is a strict workflow, nobody is allowed to push to the master branch for example, the master branch is set to being protected, which means that the only way to add to it is by making new branches and creating pull requests. These pull requests have to be reviewed by at least two people, who if everything is good, will approve the pull request. When the pull request has two approved reviews it can be rebased and merged with the master branch.
There is also an agreed upon structure to the branch names where the name must describe loosely what they are for, for example a branch named "patch-1" does not explain a lot, instead if it was called "#XX Implementing Cartographer". It first describes the issue related to the branch, and then an extremely short description of what the branch is used for.

![Git Workflow](https://github.com/D7039E-E7032E/Project-Report/blob/master/images/GitWorkflow.png)

To build the entire system the plan at the moment is to use Jenkins, this is an open-source continious integration program which means it is not directly used to build the system, but plugins can be used to add a build functionality depending on the language used. Jenkins is also a testing and reporting platform which means that it can automatically test the code after it is built.
</p>

## Meeting protocol
<p>
  <a href="https://drive.google.com/open?id=1PeUxJ5jHCUijahTg3dwKI38IQUg2Elm8">This folder</a> contains all the meeting protocols.
</p>

## Links
<p>
  <a href="https://google-cartographer-ros.readthedocs.io/en/latest/algo_walkthrough.html">Cartographer</a>  
</p>
