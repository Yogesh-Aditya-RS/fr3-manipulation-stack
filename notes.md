# Project notes

## 2026-11-23: trajectory controller test
- joint_trajectory_controller reports SUCCEEDED but the arm slumps (joints 2, 4 and 5 off by more than 0.2 rad).
- Cause: the sim ran with gazebo_effort:=true, which uses franka_gazebo_hardware (effort commands only).
- franka_arm.gazebo.xacro picks the plugin by the gazebo_effort argument. With gazebo_effort:=false it uses gz_ros2_control/GazeboSimSystem, which handles position and velocity.
- Next: launch with gazebo_effort:=false and repeat the trajectory test.
