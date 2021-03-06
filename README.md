# Perlin Noise Node

Assorted ROS nodes that implement the [perlin_noise_filter](https://github.com/jakedex/perlin_noise_filter) package to add [Perlin Noise](https://en.wikipedia.org/wiki/Perlin_noise) to the motion
of user specified joints.

Currently working with:  
  * [Aldebaran Nao](http://wiki.ros.org/nao)


*Note: Working in ROS indigo, other versions have not been tested*

## Getting Started

##### Filter Configuration

See [perlin_noise_filter](https://github.com/jakedex/perlin_noise_filter#getting-started) for instructions on configuring the Perlin Noise Filter.

##### Parameter Server

perlin_noise_node uses the [Parameter server](http://wiki.ros.org/Parameter%20Server) to retrieve configuration parameters at runtime. perlin_noise_node sets these parameters in the `.launch` file that then loads a YAML file with the command-line tool, [rosparam](http://wiki.ros.org/rosparam). Ultimately, this allows the user to quickly tweak commonly used variables such as `freq`, `speed`, and `perlin_joints` (the joints that Perlin noise is being applied to) without having to recompile the whole project.

### Existing Robot Instructions

If the robot you're working with already has a perlin_noise_node configuration, you're in luck.
First, feel free to tweak the parameter server configuration in `launch/robotName.yaml` and change any values to your liking.  

Next, you'll need to start `roscore`

```
roscore
```

Launch your robot's corresponding perlin_noise_node

```
roslaunch perlin_noise_node robotName.launch
```

*Nao only*: Enable joint stiffness

```
rosservice call /nao_robot/pose/body_stiffness/enable "{}"
```


### New Robot Instructions

New robots require the following:
  * [perlin_base.cpp implementation](#perlin_basecpp-implementation)
  * [Parameter Server configuration](#parameter-server-configuration)
  * [Launch file](#launch-file)

#### perlin_base.cpp implementation

`perlin_base.cpp` and it's corresponding header file, `perlin_base.h` contain the majority of setup functionality for each robot's node. This includes initializing the filter chain (perlin_noise_filter setup), and setting the node parameters contained in the robot's .launch file. `perlin_base` is also an [abstract base class](http://www.cplusplus.com/doc/tutorial/polymorphism/) that each node is to inherit from and implement it's pure virtual function, `timerCallback`.


Essentially, `timerCallback` should contain the implementation specific code that calls the Perlin Noise filter and publishes the results in a new message.

*See [nao.cpp](src/nao.cpp) for an example of implementing `perlin_base`*

#### Parameter Server configuration

A YAML file that will be loaded with [rosparam](http://wiki.ros.org/rosparam) should be created and include the following parameters. You're free to add any robot-specific parameters, also.

* `freq (double, default: Required)`  

  Frequency of message publishing

* `speed (double, default: Required)`  

  Motion speed of robot

* `perlin_joints (std::Vector<std::string>, default: Required)`  

  Joint names that Perlin noise will be applied to

* `joint_names (std::Vector<std::string>, default: Required)`  

  All joints names (required for message being published)

###### Example Configuration

```yaml
freq: .5
speed: .025
perlin_joints: ['HeadYaw', 'HeadPitch']
joint_names: ['HeadYaw', ...., 'RHand']
```

#### Launch file

[roslaunch](http://wiki.ros.org/roslaunch) allows for easy launching of multiple ROS nodes, as well as setting parameters on the Parameter server with the YAML configuration file described above. You'll need to create a `.launch` file to launch your robot's node and set the Parameter server parameters.

###### Example Configuration

```xml
<launch>
  <!-- The C++ nao_pn_gen node will publish a message with perlinized motion. -->
  <node pkg="perlin_noise_node" type="nao_pn_gen" name="nao_pn_gen">
    <rosparam command="load" file="$(find perlin_noise_node)/launch/nao.yaml" />
  </node>

  <!-- Load default values into Filter Chain. Add additional/custom robot launch configs below -->
  <rosparam command="load" file="$(find perlin_noise_node)/launch/filter.yaml" />
</launch>
```

## Installing

*Note: A working catkin workspace is required for installation. For more information, see [catkin](http://wiki.ros.org/catkin).*


Make sure you have sourced the correct `setup.bash` file for your ROS distribution already

Go to workspace src directory

```
cd /path/to/your/catkin_ws/src
```

Checkout the perlin_noise_node repository

```
git clone https://github.com/jakedex/perlin_noise_node
```

Then make sure you have all dependencies installed

```
cd /path/to/your/catkin_ws
rosdep install --from-paths src --ignore-src --rosdistro indigo
```

Now build

```
catkin_make
```

After sourcing /path/to/your/catkin_ws/devel/setup.bash you should now be able to use the perlin_noise_node package.

## License

This project is licensed under the BSD License - see the [LICENSE.txt](LICENSE.txt) file for details
