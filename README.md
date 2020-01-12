# Collaborative mapping of unknown environments

## Introduction 
<p>
Unknown subterranean areas hold a certain risk for humans to discover and survey. To minimize this risk one can apply the use of modern-day technology of robotics. By sending in a ground moving robot that scans the subterranean areas and then relays the scanned data back to the central station, it would allow workers to more safely traverse these previously unknown areas.
This is the base for the project, and the goal is to develop software that can be applied to the existing ground moving robots. This software should allow the robot to collaborate and map unknown areas such as caves and tunnels. The team assigned to the project consisted of seven students from the Luleå University of Technology. Each student with a background in Computer Science, Electrical Engineering and Control theory. The project lasted for a span of 20 weeks, with weekly progress updates and meetings scheduled.
</p>

## Provided Hardware and Software

### The Turtlebot
<p>
The robotic hardware used for the project was a Turtlebot 3 Burger (TB3B), provided by the robotics department within Luleå University of Technology for its size and the components.
The TB3B layout consisted of 4 stacked layers, each with their own set of components. The first (base) layer held the two DYNAMIXEL XL430-W250 actuators and a slot for the lithium polymer battery. By having two actuators the TB3B would be able to traverse at a higher speed with less stress and gain more freedom during turns.
The second layer held the OpenCR1.0 board that worked as the middleman between the actuators, battery and the Raspberry Pi. Its main purpose was to control the actuators for the TB3B, but it also helps the power switch and distribution for the system.
The third layer held the brain of the system, the Raspberry Pi 3 Model B. The Raspberry Pi was installed with a Ubuntu MATE 18.04 operating system, which handled the backend software for sending the robots gathered data to the central station, and the navigation system, which made sure that the robot did explore an already explored area.
On the final top layer were the eyes of the robot, a 360° Laser Distance Sensor LDS-01 LiDAR. The LiDAR constantly scanned and gathered data about its surroundings which were sent down to the Raspberry Pi for processing.
</p>

### The ROS Framework
<p>
The Robot Operating System (ROS) is a set of software packages and tools that help out with the creation of robotic applications. ROS provides libraries that support a vast amount functionality one could desire from a robot e.g. navigation and simultaneous localization and mapping (SLAM). These two features were used extensively within the project.
ROS also provides tools for simulation and visualization. These tools were used to debug the application and test if desired behaviour had been reached before the application was deployed into a real world scenario for further benchmarking.
</p>

<p>
A ROS application is made up of several nodes in a network called a ROS graph where each node is a process that performs computation. Nodes are meant to operate on a very granular scale and a ROS application will therefore comprise of many nodes. In the case with the TB3B there is for example one node controlling the LIDAR, one controlling the motors to the wheels and one node performing localization.
</p>

<p>
Nodes communicate with each other by publishing messages to topics which are then subscribed to by other nodes. A message can contain a range of different data types. Standard primitive types are supported as are arrays of them.
</p>

<p>
Messages also come in the form of service calls where a node sends a request message to a node in order to recieve a reply in the form of a response message.
</p>

<p>
Topics are named buses which nodes send messages over. Topics generate messages even if there is no subscriber. Nodes do not know who they are communicating with, it works on the same principle as FM radio where the interested will tune themselves to the continous broadcast to find the information they are interested in.
</p>

<p>
This model is however not appropriate for request and reply interactions between nodes. This is therefore done with another concept called a service. These can often be treated as if they wre remote procedure calls.
</p>

## Method

### The controller
<p>
The idea will be to use a [linear controller](https://github.com/D7039E-E7032E/Project-Report/wiki/Path-following-control) to make sure the Turtlebot 3 Burger is staying on the path in the physical world.
</p>

### The simulator
<p>
The software that will visualize what the robot experiences is RViz. This software will also be chosen because of its tight integration with the ROS framework.
A TB3B will be simulated using the simulation software Gazebo. The reason this software will be chosen is because it is tightly integrated with the ROS framework.
</p>

<p>
The software Rviz will make use of a file format called Universal Robotic Description Format (URDF). A URDF file describing the TB3B will be used to describe the robot and its capabilities to Rviz. A camera will be added to the robot and therefore a camera will have to be added to the URDF describing the robot. URDF files will be written in XML but by using the macro language xacro. This will make the URDF files easier to maintain and to read. URDF can be visualized using the URDF package that exists within ROS. This could prove useful to ensure the camera is connected to the body of the robot.
</p>

<p>
The software Gazebo made use of a file format called Simulation Description Format (SDF). SDF was developed to solve the shortcomings of URDF. SDF specifies additional properties such as friction, lights, heightmaps etc. Another improvement that SDF has is that it does not break proper formatting with heavy use of XML attributes and is thus more flexible in its implementation. 
It is possible to convert URDF into SDF. This allows a URDF file be used with Gazebo after it has been converted to SDF. This process was carried out automatically.
</p>

<p>
ROS makes use of topics to send messages from a publisher to a subscriber. Gazebo published simulation messages which were computed within the ROS framework and then published to RViz for visualization.
The first tests were carried out by following the tutorials available for the the TB3B.
The goal was first to make sure that the camera had been implemented correctly and thus Rviz was made to only visualize the camera feed.
The script that described what to visualize was extended significantly as the project went on.
</p>

<p>
By using Rviz, a 2D goal point was successfully published to the TB3B and all relevant topics were recieved correctly.
By following the earlier mentioned tutorials for the TB3B it became possible to navigate on a known map in a simulated world with the modified TB3B model.
</p>

### Data gathering
<p>
The LIDAR was used to gather all the data used to map the environment. The raw data was processed by the Cartographer algorithm within the Raspberry Pi, to generate a temporary map file and an accessory data file to the map. These two files were then sent onward to the central station.
</p>

### Cartographer
<p>
The Cartographer system is derived from Google’s repository in GitHub where the system is an open-source project. This system’s main focus is to be a solution to the simultaneous localization and mapping (SLAM) problem that is about how one can do both localization and mapping at the same time. 
</p>

<p>
Cartographer is an algorithm used to get a representation of the local environment given useful sensor data. For localization and mapping of the local environment, SLAM solving is the main reason to use Cartographer. It contains two SLAMs, a local SLAM, and a global SLAM. The local SLAM's function is to generate approved submaps with correct scans (range data) and the global SLAM combines all submaps and creates a global map. The Global map can then be used to visually represent the environment.

The usefulness of this system is in the quality of representing the environment, high-resolution maps can be easily created with this system. Cartographer itself creates a compressed file (pbstream) as an output. The file only contains what Cartographer claims to be useful sensor data. Then when recombined with the full sensor data file (bag) creating a high-resolution map. Although it is in the interest of the project to combine multiple maps from multiple robots into one map, there was no clear way to do that with the system. Instead, the output file in interest became a portable grey map (PGM file). For Cartographer to work with the rest of the parts, necessary tuning on the system’s parameters had to be done to work with the turtlebot3.  

</p>

### Navigation
The Cartographer ROS node            |  The Navigation Stack
:-------------------------:|:-------------------------:
![RQT graph of the Cartographer ROS node](https://google-cartographer-ros.readthedocs.io/en/latest/_images/nodes_graph_demo_2d.jpg)  |  ![Navigation Stack Setup (Source: ros.org)](http://wiki.ros.org/navigation/Tutorials/RobotSetup?action=AttachFile&do=get&target=overview_tf_small.png)


<p>
The navigation system comprised SLAM provided by the Cartographer online node and a library of navigation packages known as the ROS Navigation Stack [2]. The submap list of Cartographer was sent as map data into the stack and then globally and locally planned upon. The local planner that was used in the first tests was the default Dynamic Window Approach Planner (DWAP) but later in the project, a new local planner was innovated and implemented into the ROS Navigation Stack.
The global plan was generated by the default global planner known simply as global_planner.
</p>

### Control

The robot needs to be able to navigate unknown environments, a robust and reliable control system is needed to achieve this.

![](https://github.com/D7039E-E7032E/Project-Report/blob/Controll-delen-till-rapport/images/Control_Str.svg)

In the illustration above the structure of the control system an its supporting components. 

#### Global Navigator

The purpose of the global navigator is to find a path the robot should follow to reach its goal. The the goal points are set manually and when the robot reaches it, a new one will be chosen by the operator. It will use information from the global map, a map of the combined scans from all robots. This was implemented using the already existing ROS package move_base that calculates a global path for the robot using Dynamic Window Approach Planner algorithm. 

The Global Navigator uses maps generated from cartographer, which is a slow process, combined with A-star not being a particular fast algorithm. This makes the Global Navigator a rather slow. This is solved with a Local Navigator.

#### Local Navigator

The Local Navigator's purpose is to avoid obstacles and keeping an optimal distance to walls, this to give optimal scanning quality. It will follow the direction of the path given by the Global Navigator.

![Force](https://github.com/D7039E-E7032E/Project-Report/blob/Controll-delen-till-rapport/images/Force.svg)

A custom ROS node was implemented to solve this. The node operates by giving all point seen by the lidar a virtual force, see equation above, which when combined creates an heat map. The node then solves the gradient of the heat map in the position of the robot, as in equation below. The value of *l<sub>p</sub>* is the distance to the point, and the values *c* and *n* is tuning-parameters, which was tuned manually. The resulting vector will then point in a direction away from all obstacles.

![Grad](https://github.com/D7039E-E7032E/Project-Report/blob/Controll-delen-till-rapport/images/Grad.svg)

Finely the gradient vector is combined with a directional vector which is the direction that the path is currently pointing. The resulting vector, if followed will take the robot toward its goal while avoiding obstacles and keep an equal distance to the walls on both sides.

#### Path Follower

The Path Follower is a controller that control the speed of the robots actuators.

It follows the direction given by the Local Navigator such that the point P in the figure below, is always facing the correct direction. The control system will achieve this by changing speed relation between the wheels on the robot, making it turn.

<figure>
    <img src="https://github.com/D7039E-E7032E/Project-Report/blob/fc1c1424542d7d816bf94292356d8a6f613ad361/RobotRouteFolowC.svg">


</figure>

##### System 

To simplify the system, some assumptions were made. Because the robot is equipped with strong motors and wheels with a good grip, the assumption was made that the robot will not lose it's grip. The system is also assumed to have a near perfect step response. With these assumptions the system will be static, only changing when moved by actuator.

![systeI](https://github.com/D7039E-E7032E/Project-Report/blob/fc1c1424542d7d816bf94292356d8a6f613ad361/systeI.svg) 

By making the final assumption that the target point T, in the figure above, is stationary then we can introduce a new variable d as in equation below.

![Asume](https://github.com/D7039E-E7032E/Project-Report/blob/fc1c1424542d7d816bf94292356d8a6f613ad361/Asume.svg) 

Then the system can be represented in a state space model seen in equation below, where Wmax is the maximum rotation speed of the robot.

![system](https://github.com/D7039E-E7032E/Project-Report/blob/fc1c1424542d7d816bf94292356d8a6f613ad361/system.svg) 

##### Control rule

Because the system is of first order, by using the assumptions from the previous section, the optimal controller will be a P controller, as seen in equation below.

![rule](https://github.com/D7039E-E7032E/Project-Report/blob/fc1c1424542d7d816bf94292356d8a6f613ad361/controlrool.svg) 

The variable p, in equation above, was chosen in simulations for optimal performance.




### Central station
<p>
In this case, Central station is the ROS master. Its role is to look over the other active nodes and help them locate each other. ROS master also comes with a parameter server which will store and retrieve parameters from nodes. These parameters are XMLRPC data types which are XML equivalents of common data types.

  
</p>

## Results
<p>
</p>

## Discussion
<p>
As Cartographer is an approach to this dilemma, different approaches such as the Hector SLAM approach and Gmapping SLAM could have been tested. Due to the amount of time consumed to get to understand, implement and tune Cartographer, there would be insufficient time to do the rest. 
</p>

# Appendices

## Gantt Schedule
[Gantt Schedule](https://htmlpreview.github.io/?https://github.com/D7039E-E7032E/Project-Report/blob/master/GANTT/ProjectPlan-chart.html)

### Workflow and Git
<p>
When researching, the main points of the read article should be written down in the WIKI, this is in case someone else has to read up on the same thing. This means that they will not have to read the whole article to learn the main points, also if any important commands are included, they will be written down with a description of what they do.
  
In Git there is a strict workflow, nobody is allowed to push to the master branch, for example, the master branch is set to be protected, which means that the only way to add to it is by making new branches and creating pull requests. These pull requests have to be reviewed by at least two people, who if everything is good, will approve the pull request. When the pull request has two approved reviews it can be rebased and merged with the master branch.
There is also an agreed-upon structure to the branch names where the name must describe loosely what they are for, for example, a branch named "patch-1" does not explain a lot, instead if it was called "#XX Implementing Cartographer". It first describes the issue related to the branch, and then an extremely short description of what the branch is used for.

![Git Workflow](https://github.com/D7039E-E7032E/Project-Report/blob/master/images/GitWorkflow.png)

To build the entire system the plan at the moment is to use Jenkins, this is an open-source continuous integration program which means it is not directly used to build the system, but plugins can be used to add a build functionality depending on the language used. Jenkins is also a testing and reporting platform which means that it can automatically test the code after it is built.
</p>

## Meeting protocol
<p>
  All the meeting protocols can be found<a href="https://github.com/D7039E-E7032E/Project-Report/wiki/Meeting-protocols"> here</a>.
</p>

## Links
<p>
  <a href="https://google-cartographer-ros.readthedocs.io/en/latest/algo_walkthrough.html">Cartographer</a>
</p>
<p>
<a href="https://github.com/D7039E-E7032E/Project-Report/wiki/Path-following-control">Path following control</a><br/>
</p>
<p>
<a href="https://developers.google.com/protocol-buffers/">Protocol buffer</a><br/>
</p>
