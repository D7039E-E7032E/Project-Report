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
</p>

### Workflow and Git
<p>
When researching, the main points of the read article should be written down in the WIKI, this is in case someone else has to read up on the same thing. This means that they will not have to read the whole article to learn the main points, also if any important commands are included, they will be written down with a description of what they do.
  
In Git there is a strict workflow, nobody is allowed to push to the master branch for example, the master branch is set to being protected, which means that the only way to add to it is by making new branches and creating pull requests. These pull requests have to be reviewed by at least two people, who if everything is good, will approve the pull request. When the pull request has two approved reviews it can be rebased and merged with the master branch.
There is also an agreed upon structure to the branch names where the name must describe loosely what they are for, for example a branch named "patch-1" does not explain a lot, instead if it was called "#XX Implementing Cartographer". It first describes the issue related to the branch, and then an extremely short description of what the branch is used for.

To build the entire system the plan at the moment is to use Jenkins, this is an open-source continious integration program which means it is not directly used to build the system, but plugins can be used to add a build functionality depending on the language used. Jenkins is also a testing and reporting platform which means that it can automatically test the code after it is built.
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

## Meeting protocol
<p>
  <a href="https://drive.google.com/open?id=1PeUxJ5jHCUijahTg3dwKI38IQUg2Elm8">This folder</a> contains all the meeting protocols.
</p>

## Links
<p>
  <a href="https://google-cartographer-ros.readthedocs.io/en/latest/algo_walkthrough.html">Cartographer</a>  
</p>
<p>
  <a href="https://google-cartographer-ros.readthedocs.io/en/latest/algo_walkthrough.html">Cartographer</a>  
</p>
