# Project_ws
Ros-projects
# ROS Three Wheeled Robot Navigation Project
## The steps involved in this project have been described in detail as follows:-

1. **Modeling in fusion 360 and Converting into URDF file to run in GAZEBO simulator.**
2. **Gazebo Plugins**
3. **Intro to R-viz and RQT graphs**
4. **SLAM (Simultaneous localization and mapping)**
5. **Navigation Stack to Self-design robot**

## **What is this script?**

This is a fusion 360 script to export urdf from fusion 360 directly.

This exports:

- .urdf file of your model
- .launch and .yaml files to simulate your robot on the gazebo
- .stl files of your model

### **Sample**

•  I used fusion 360 software for designing and developing the robot and then that design model has been converted into a URDF file.

### **original model**

![Screenshot 2023-05-09 175256](https://github.com/Vikeesalunkhe/Project_ws/assets/117392336/cc4ec390-cf12-4d7a-a406-0a67b54200cf)

### **Gazebo simulation of exported .urdf and .launch**

![image](https://user-images.githubusercontent.com/101028751/192140699-0ff8a9b8-8446-42cc-8278-f7e041d5b898.png)

### Information about the plugin

Adding the laser scan plugin to your robot can give it a sense of perception of the surrounding world. It simulates a Lidar on top of your robot.
Open your exported URDF file, and navigate to the robot.gazebo file and add the plugin there.

### Raw Plugin Code

```xml
<gazebo reference="hokuyo_link">
    <sensor type="gpu_ray" name="head_hokuyo_sensor">
      <pose>0 0 0 0 0 0</pose>
      <visualize>false</visualize>
      <update_rate>40</update_rate>
      <ray>
        <scan>
          <horizontal>
            <samples>720</samples>
            <resolution>1</resolution>
            <min_angle>-1.570796</min_angle>
            <max_angle>1.570796</max_angle>
          </horizontal>
        </scan>
        <range>
          <min>0.10</min>
          <max>30.0</max>
          <resolution>0.01</resolution>
        </range>
        <noise>
          <type>gaussian</type>
          <!-- Noise parameters based on published spec for Hokuyo laser
               achieving "+-30mm" accuracy at range < 10m.  A mean of 0.0m and
               stddev of 0.01m will put 99.7% of samples within 0.03m of the true
               reading. -->
          <mean>0.0</mean>
          <stddev>0.01</stddev>
        </noise>
      </ray>
      <plugin name="gazebo_ros_head_hokuyo_controller" filename="libgazebo_ros_gpu_laser.so">
        <topicName>/rrbot/laser/scan</topicName>
        <frameName>hokuyo_link</frameName>
      </plugin>
    </sensor>
  </gazebo>
```

    
### Test your Robot

Now, that we have configured the robot with the parameters, let's test whether things are working perfectly.
(Please change the name of the package as required)

- Start the display.launch file
    
    ```bash
    roslaunch differential_drive_robot_version_1_description display.launch
    ```
    
- Start the gazebo.launch file
    
    ```bash
    roslaunch differential_drive_robot_version_1_description gazebo.launch
    ```
    
- Setting up Visualization
    
    Goto RVIZ and click on the add section.
    Then click on By topic
    Find the /scan dropdown and click on the LaserScan option. 
    Finally, Click OK
    
   ![Screenshot from 2023-12-24 23-40-38](https://github.com/Vikeesalunkhe/Project_ws/assets/117392336/ca8fda5d-f766-46b9-8926-cf095d0bb10b)    
- Visualizing the Environment
    
    Even after completing the Setup, nothing can be seen on RVIZ,
    because there is nothing in the environment to visualize.
    Let's add a few objects from the Gazebo Simulation environment.
    Once this is done, We can finally visualize objects around our robot.
    
    ![Objects dropped in Gazebo](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ec7d7c03-757a-4c1b-9718-2eea4d75447c/Screenshot_from_2022-08-15_13-54-24.png)
    
    Objects dropped in Gazebo
    
    ![Objects being visualized in RVIZ](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/de908e44-8d1f-457b-9f0f-4b0913d49988/Screenshot_from_2022-08-15_13-54-43.png)
    
    Objects being visualized in RVIZ
    

### Further reading / References:

Reference:   [Gazebo plugins in ROS](http://gazebosim.org/tutorials?tut=ros_gzplugins) 

### Information about the plugin

Adding the differential drive plugin to your robot can enable you to do teleoperation and lays the foundation for autonomous navigation.
Open your exported URDF file, and navigate to the robot.gazebo file and add the plugin there.

### Raw Plugin Code

```xml
<gazebo>
  <plugin name="differential_drive_controller" filename="libgazebo_ros_diff_drive.so">

    <!-- Plugin update rate in Hz -->
    <updateRate>${update_rate}</updateRate>

    <!-- Name of left joint, defaults to `left_joint` -->
    <leftJoint>base_link_left_wheel_joint</leftJoint>

    <!-- Name of right joint, defaults to `right_joint` -->
    <rightJoint>base_link_right_wheel_joint</rightJoint>

    <!-- The distance from the center of one wheel to the other, in meters, defaults to 0.34 m -->
    <wheelSeparation>0.5380</wheelSeparation>

    <!-- Diameter of the wheels, in meters, defaults to 0.15 m -->
    <wheelDiameter>0.2410</wheelDiameter>

    <!-- Wheel acceleration, in rad/s^2, defaults to 0.0 rad/s^2 -->
    <wheelAcceleration>1.0</wheelAcceleration>

    <!-- Maximum torque which the wheels can produce, in Nm, defaults to 5 Nm -->
    <wheelTorque>20</wheelTorque>

    <!-- Topic to receive geometry_msgs/Twist message commands, defaults to `cmd_vel` -->
    <commandTopic>cmd_vel</commandTopic>

    <!-- Topic to publish nav_msgs/Odometry messages, defaults to `odom` -->
    <odometryTopic>odom</odometryTopic>

    <!-- Odometry frame, defaults to `odom` -->
    <odometryFrame>odom</odometryFrame>

    <!-- Robot frame to calculate odometry from, defaults to `base_footprint` -->
    <robotBaseFrame>base_footprint</robotBaseFrame>

    <!-- Odometry source, 0 for ENCODER, 1 for WORLD, defaults to WORLD -->
    <odometrySource>1</odometrySource>

    <!-- Set to true to publish transforms for the wheel links, defaults to false -->
    <publishWheelTF>true</publishWheelTF>

    <!-- Set to true to publish transforms for the odometry, defaults to true -->
    <publishOdom>true</publishOdom>

    <!-- Set to true to publish sensor_msgs/JointState on /joint_states for the wheel joints, defaults to false -->
    <publishWheelJointState>true</publishWheelJointState>

    <!-- Set to true to swap right and left wheels, defaults to true -->
    <legacyMode>false</legacyMode>
  </plugin>
</gazebo>
```

As this is a standard plugin, we have to change a few parameters to make it work with our robot.

### Changes to be made

- Plugin Update Rate
    
    Modify the value of the Update rate to 10 Hz
    
- Joint names
    
    Correspondingly update the joint name for Left Joint and Right Joint
    To find the respective names of the joints, navigate to the robot.xacro file. 
    
- Wheel Parameters
    
    Update the value of Wheel Separation ( the distance from the center of one wheel to the center of the other) in meters.
    Update the value of Wheel Diameter in meters.
    Both these parameters are crucial in simulating the dynamics of the mobile robot.
    
- Update the robot Base Frame
    
    The robotBaseFrame points to the 'base_link' of our robot (chassis).
    So just change the value from 'base_footprint' to 'base_link'
    

### Test your Robot

Now, that we have configured the robot with the parameters, let's test whether things are working perfectly.
(Please change the name of the package as required)

- Start the gazebo.launch file
    
    ```bash
    roslaunch differential_drive_robot_version_1_description gazebo.launch
    ```
    
- Start the display.launch file
    
    ```bash
    roslaunch differential_drive_robot_version_1_description display.launch
    ```
    
- Start the Teleoperation node
    
    ```bash
    rosrun teleop_twist_keyboard teleop_twist_keyboard.py
    ```
    

### Further reading / References

Reference:   [Gazebo plugins in ROS](http://gazebosim.org/tutorials?tut=ros_gzplugins) 

### What is SLAM?

SLAM stands for Simultaneous Localization and Mapping.
The problem statement stands to construct a map of an unknown environment while traversing through it and keeping track of the robot's current location with respect to the articulated environment. SLAM is widely used in self-driving cars, unmanned aerial vehicles, autonomous underwater vehicles, planetary rovers, etc.

![image](https://user-images.githubusercontent.com/101028751/192140741-496474a9-17fb-4ac6-8308-187494f078d7.png)

### Terminologies

<aside>
💡 **Localization**
Robot localization is the process of determining where a mobile robot is located with respect to its environment. Localization is one of the most fundamental competencies required by an autonomous robot as the knowledge of the robot's own location is an essential precursor to making decisions about future actions.

</aside>

<aside>
💡 **Mapping**
Mapping simply means creating a map or floor plan of the current environment. The maps generated can be of many different types, but the fundamental goal is to capture the location of objects surrounding the robot. The map can later be used for various purposes like Localization of robots, path planning, etc.

</aside>

<aside>
💡 **2D Occupancy Grid**
A special form of a map that is extensively used in Robotic Mapping applications. It addresses the problem of generating maps from noisy and uncertain sensor measurement data, with the assumption that the robot pose is known. In the occupancy grid map, the environment is an evenly spaced field of binary random variables each representing the presence of an obstacle at that location in the environment.

</aside> 

### Installation of required packages

- Installing the Gmapping package
    
    ```bash
    sudo apt-get install ros-<destro>-gmapping
    ```
    
- Installing the Map Server package
    
    ```bash
    sudo apt-get install ros-<destro>-map-server
    ```
    

### Implementing SLAM

- Start the gazebo.launch file
    
    ```bash
    roslaunch differential_drive_robot_version_1_description gazebo.launch
    ```
    
- Start the display.launch file
    
    ```bash
    roslaunch differential_drive_robot_version_1_description display.launch
    ```
    
- Run the Gmapping Node
    
    Raw Command:
    
    ```bash
    rosrun gmapping slam_gmapping scan:={scan Topic}
    ```
    
    Modified Command:
    
    ```bash
    rosrun gmapping slam_gmapping scan:=/scan
    ```
    
- Setting up Map in RVIZ
    
    Goto RVIZ and click on the add section.
    Then click on By topic
    Find the /map dropdown and click on the map option. 
    Finally, Click OK
    
- Mapping the Environment
    
    After completing the Setup, mapping starts and can be seen on RVIZ.
    As the robot moves around the environment it creates a better map of the surrounding area.
    Once the robot has explored quite a lot of the world using teleoperation, the map is well defined and ready to be saved for further use.
    
- Saving the map
    
    Once the map is well defined, we can use the map_server package to save our map.
    This map can later be passed to our Navigation stack.
    To save the map, run the command in a new terminal after making appropriate changes.
    
    Raw Command
    
    ```bash
    rosrun map_server map_saver -f ~/catkin_ws/src/{pkg_name/location}/map_name
    ```
    
### What is Navigation?

Autonomous Navigation means that a robot can traverse from one point to another without any human intervention. In order to do the same, a robot has to perform complex maneuvers like path planning, localization, dynamic obstacle avoidance, etc. A robot uses many sensors, algorithms, models, etc to help it achieve this amazing feat.
To successfully implement Autonomous Navigation on a system, there can be many approaches.
One such approach is discussed here where a Nav Stack is created using various things like AMCL, Move_Base, Map_Server, sensor, and odometry data.

**RQT graph** 

![image](https://user-images.githubusercontent.com/101028751/192140762-6351bc87-90d1-4753-b4d1-0223ee759e23.png)


### Installation of required packages

- Installing the Map Server package
    
    ```bash
    sudo apt-get install ros-<destro>-map-server
    ```
    
- Installing the AMCL package
    
    ```bash
    sudo apt-get install ros-<destro>-amcl
    ```
    
- Installing the Move_Base package
    
    ```bash
    sudo apt-get install ros-<destro>-move-base
    
    sudo apt-get install ros-<destro>-dwa-local-planner
    ```

    
- Create **navigation.launch**
    
    We need a new launch file that can launch a few specific nodes and other launch files.
    So we will create a new launch file with the name navigation.launch
    Firstly, we add details to launch amcl.launch file.
    Next, we add details for launching the move_base.launch file.
    Then, we add details for launching the map_server node and supply a map file to it.
    Finally, we add the details to launch the Rviz node. 
    
    Refer to the navigation.launch file for reference.
    


- Start the **gazebo.launch** file
    
    ```bash
    roslaunch differential_drive_robot_version_1_description gazebo.launch
    ```
    
- Start the **navigation.launch** file
    
    ```bash
    roslaunch differential_drive_robot_version_1_description navigation.launch
    ```
    

### Further reading / References

[http://wiki.ros.org/navigation/Tutorials/RobotSetup](http://wiki.ros.org/navigation/Tutorials/RobotSetup)

[https://www.researchgate.net/publication/349602009_An_Extended_Analysis_on_Tuning_The_Parameters_of_Adaptive_Monte_Carlo_Localization_ROS_Package_in_an_Automated_Guided_Vehicle/fulltext/6037cd1a4585158939cd8ec5/An-Extended-Analysis-on-Tuning-The-Parameters-of-Adaptive-Monte-Carlo-Localization-ROS-Package-in-an-Automated-Guided-Vehicle.pdf](https://www.researchgate.net/publication/349602009_An_Extended_Analysis_on_Tuning_The_Parameters_of_Adaptive_Monte_Carlo_Localization_ROS_Package_in_an_Automated_Guided_Vehicle/fulltext/6037cd1a4585158939cd8ec5/An-Extended-Analysis-on-Tuning-The-Parameters-of-Adaptive-Monte-Carlo-Localization-ROS-Package-in-an-Automated-Guided-Vehicle.pdf)

[http://wiki.ros.org/amcl](http://wiki.ros.org/amcl)

[http://wiki.ros.org/navigation/RobotsUsingNavStack](http://wiki.ros.org/navigation/RobotsUsingNavStack)

[TurtleBot](https://github.com/ROBOTIS-GIT/turtlebot3)
