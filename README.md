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

### Cartographer
<p>
The Cartographer system is derived from Google’s repository in GitHub where the system is an open-source project. This system’s main focus is to be a solution to the simultaneous localization and mapping (SLAM) problem that is about how one can do both localization and mapping at the same time. 
</p>

<p>
Cartographer is an algorithm used to get a representation of the local environment given useful sensor data. For localization and mapping of the local environment, SLAM solving is the main reason to use Cartographer. It contains two SLAMs, a local SLAM, and a global SLAM. The local SLAM's function is to generate approved submaps with correct scans (range data) and the global SLAM combines all submaps and creates a global map. The Global map can then be used to visually represent the environment.
</p>

<p>
The usefulness of this system is in the quality of representing the environment, high-resolution maps can be easily created with this system. Cartographer itself creates a compressed file (pbstream) as an output. The file only contains what Cartographer claims to be useful sensor data. Then when recombined with the full sensor data file (bag) creating a high-resolution map. Although it is in the interest of the project to combine multiple maps from multiple robots into one map, there was no clear way to do that with the system. Instead, the output file in interest became a portable grey map (PGM file). For Cartographer to work with the rest of the parts, necessary tuning on the system’s parameters had to be done to work with the turtlebot3.  
</p>

### Detecting lines in image
<p>
There are several steps to do when it comes to detecting lines in an image. For starters, only the necessary data such as the edges on the map representing the walls was wanted. The research paper (source) used the Canny Edge Detection approach to detect the edges in an image. Canny Edge Detection is a multi-step algorithm where the goal is to create a binary representation of the image where ones are walls and zeros are everything else. First, it’s using gaussian blur as filtering out noises before running what is known as Sobel edge detection. This ensures that much of the unwanted edges are removed. From here threshold and hysteresis are applied to result in the binary image with only the edges left.
</p>

<p>
When the newly created image with Canny Edge is done, Hough Transform was the next step. Hough Transform takes advantage of mapping each pixel coordinates to a corresponding line in Hough Space. Since each pixel has a fixed known X and Y coordinate value which can be transformed into a polar coordinate with ⍴ and θ as the axis.
</p>

*⍴=Xcos(θ)+Ysin(θ)*

<p>
Thus each pixel represents a sinusoid line in Hough Space. When each pixel gets a representation in Hough Space there will be a bunch of intersection points where the lines meet. These intersection points in Hough Space mapped back to the Image Space gives the most common lines in the image. By here the walls have been detected.
</p>

## Method

### System design
<p>
The project has two major components in its design, the Turtlebot and the Central station. As illustrated in figure ... both the Turtlebot and the Central station holds their own internal components.
</p>

![System design](https://github.com/D7039E-E7032E/Project-Report/blob/master/images/Final_design.png)

<p>
As illustrated in figure ... the Turtlebot has sensor data that feeds into the Raspberry Pi. The Raspberry Pi has the cartographer algorithm running on it. Cartographer forwards its generated map into the global costmap. The map is also saved from cartographer and transferred through SSH SCP to the Central station.
</p>

<p>
The Central station takes care of the merging process. Once complete it will send the merged map back to the Turtlebot through SSH SCP, where the global costmap recieves it.
</p>

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

### Navigation
The Cartographer ROS node            |  The Navigation Stack
:-------------------------:|:-------------------------:
![RQT graph of the Cartographer ROS node](https://google-cartographer-ros.readthedocs.io/en/latest/_images/nodes_graph_demo_2d.jpg)  |  ![Navigation Stack Setup (Source: ros.org)](http://wiki.ros.org/navigation/Tutorials/RobotSetup?action=AttachFile&do=get&target=overview_tf_small.png)


<p>
The navigation system comprised SLAM provided by the Cartographer online node and a library of navigation packages known as the ROS Navigation Stack [2]. The submap list of Cartographer was sent as map data into the stack and then globally and locally planned upon. The local planner that was used in the first tests was the default Dynamic Window Approach Planner (DWAP) but later in the project, a new local planner was innovated and implemented into the ROS Navigation Stack.
The global plan was generated by the default global planner known simply as global_planner.
</p>

### Central station
<p>
Central station's purpose is to control and communicate with the TB3Bs. From the central station, all packages are runned through the scripts which contains the essential ROS commands. Also the ROS master node is started through ROS command roscore at central station. Its role is to look over the other active nodes and help them locate each other. ROS master also comes with a parameter server which will store and retrieve parameters from nodes. These parameters are XMLRPC data types which are XML equivalents of common data types.
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
