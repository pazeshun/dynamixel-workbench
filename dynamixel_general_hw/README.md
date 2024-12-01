# dynamixel_general_hw

General ros_control layer for Dynamixel actuators. With this layer, you can load ros_control controllers (e.g., [joint_trajectory_controller](http://wiki.ros.org/joint_trajectory_controller)) and make the actuators follow commands from those controllers.

## Samples

### Sample 1: simplest situation

This sample assumes that one bare Dynamixel actuator whose ID and baud rate are `1` and `57600` is connected via a port `/dev/ttyUSB0`.
You can change the baud rate and the port via roslaunch arguments.

```bash
roslaunch dynamixel_general_hw sample1.launch port_name:=/dev/ttyUSB0 baud_rate:=57600
```

You will see the following RViz window showing a virtual robot synchronized with the real actuator:
<div align="left">
<img width="50%" src=".media/sample1_rviz.png">
</div>

You can move the actuator by sending a command via `/sample_robot/position_joint_trajectory_controller/follow_joint_trajectory` action.
To try sending a command,
1. Type
   ```bash
   rostopic pub /sample_robot/position_joint_trajectory_controller/follow_joint_trajectory/goal
   ```
   and enter `Tab` three times.
2. Rewrite `time_from_start` and `positions` of the auto-completed output as you want
3. Type `sample_joint` into the empty string following `joint_names` of the auto-completed output

For example, if you send a command like:
```bash
rostopic pub /sample_robot/position_joint_trajectory_controller/follow_joint_trajectory/goal control_msgs/FollowJointTrajectoryActionGoal "header:
  seq: 0
  stamp:
    secs: 0
    nsecs: 0
  frame_id: ''
goal_id:
  stamp:
    secs: 0
    nsecs: 0
  id: ''
goal:
  trajectory:
    header:
      seq: 0
      stamp:
        secs: 0
        nsecs: 0
      frame_id: ''
    joint_names:
    - 'sample_joint'
    points:
    - positions: [0.78]
      velocities: [0]
      accelerations: [0]
      effort: [0]
      time_from_start: {secs: 10, nsecs: 0}
  path_tolerance:
  - {name: '', position: 0.0, velocity: 0.0, acceleration: 0.0}
  goal_tolerance:
  - {name: '', position: 0.0, velocity: 0.0, acceleration: 0.0}
  goal_time_tolerance: {secs: 0, nsecs: 0}"
```

You get the following final state:
<div align="left">
<img width="50%" src=".media/sample1_rviz_move.png">
</div>

Note that this commanding method is only for checking on command line.
Please use [actionlib](http://wiki.ros.org/actionlib) when you write a code.

### Sample 2: with mechanical reduction and joint offset

This sample assumes that one bare Dynamixel actuator whose ID and baud rate are `1` and `57600` is connected via a port `/dev/ttyUSB0`.
You can change the baud rate and the port via roslaunch arguments.

```bash
roslaunch dynamixel_general_hw sample2.launch port_name:=/dev/ttyUSB0 baud_rate:=57600
```

You will see the following RViz window showing a virtual robot synchronized with the real actuator:
<div align="left">
<img width="50%" src=".media/sample2_rviz.png">
</div>

By launching after making the actuator position the same as Sample 1, you will notice that the joint position has an offset in comparison with Sample 1.
In addition, by sending some commands, you will notice there is a reduction between the real actuator and the virtual joint on RViz.

### Sample 3: multiple actuators

This sample assumes that two bare Dynamixel actuators whose IDs and baud rate are `1`, `2`, and `57600` are connected via a port `/dev/ttyUSB0`.
You can change the baud rate and the port via roslaunch arguments.

```bash
roslaunch dynamixel_general_hw sample3.launch port_name:=/dev/ttyUSB0 baud_rate:=57600
```

You will see the following RViz window showing a virtual robot synchronized with the real actuators:
<div align="left">
<img width="50%" src=".media/sample3_rviz.png">
</div>

For example, if you send a command like:
```bash
rostopic pub /sample_robot/position_joint_trajectory_controller/follow_joint_trajectory/goal control_msgs/FollowJointTrajectoryActionGoal "header:
  seq: 0
  stamp:
    secs: 0
    nsecs: 0
  frame_id: ''
goal_id:
  stamp:
    secs: 0
    nsecs: 0
  id: ''
goal:
  trajectory:
    header:
      seq: 0
      stamp:
        secs: 0
        nsecs: 0
      frame_id: ''
    joint_names: ['sample_pan_joint', 'sample_tilt_joint']
    points:
    - positions: [-0.78, 0.78]
      velocities: []
      accelerations: []
      effort: []
      time_from_start: {secs: 10, nsecs: 0}
  path_tolerance:
  - {name: '', position: 0.0, velocity: 0.0, acceleration: 0.0}
  goal_tolerance:
  - {name: '', position: 0.0, velocity: 0.0, acceleration: 0.0}
  goal_time_tolerance: {secs: 0, nsecs: 0}"
```
You get the following final state:
<div align="left">
<img width="50%" src=".media/sample3_rviz_move.png">
</div>

## Launch files

### dynamixel_general_control.launch

A launch file to run the ros_control layer. Launch it when Dynamixel actuators are connected to your PC.

#### Arguments

Check them by `roslaunch dynamixel_general_hw dynamixel_general_control.launch --ros-args`.

#### Minimal publishing topics

- `$(arg namespace)/dynamixel_general_control/dynamixel_state` (`dynamixel_general_hw/DynamixelStateList`)

  States of the connected actuators.

#### Minimal subscribing topics

- `$(arg namespace)/dynamixel_general_control/servo` (`std_msgs/Bool`)

  Servo ON/OFF signal. You can use this to implement an emergency stop function.

- `$(arg namespace)/dynamixel_general_control/hold_position` (`std_msgs/Bool`)

  When this is `true`, the actuators hold their current position regardless of commanded positions.
  You can also use this to implement an emergency stop function.

#### Minimal services

- `$(arg namespace)/dynamixel_general_control/dynamixel_command` (`dynamixel_workbench_msgs/DynamixelCommand`)

  Direct command to Dynamixel control table. This is the same as [/dynamixel_command service on dynamixel_workbench](https://emanual.robotis.com/docs/en/software/dynamixel/dynamixel_workbench/#controllers).