# Development Log

Newest entries at the top. Each entry: context, what I did, result, decision.

## 2026-11-23: Can MoveIt drive the FR3 in Gazebo?

**Context**
MoveIt2 sends joint *position* trajectories. I needed to confirm the simulated
FR3 accepts them through a standard `joint_trajectory_controller`.

**What I did**
1. Launched Franka's example sim (`gazebo_franka_arm_example_controller.launch.py`).
2. Deactivated `joint_impedance_example_controller`.
3. Loaded `fr3_arm_controller` (position interface) and sent a small joint-4 goal.

**Result**
- The action returned SUCCEEDED, but the arm slumped in Gazebo.
- `/joint_states` confirmed joints 2, 4 and 5 were far from their goals.
- "Goal reached" was a false positive: nothing was actually driving the joints.

**Root cause**
The example launch hardcodes `gazebo_effort: 'true'`. In
`franka_arm.gazebo.xacro` that selects `franka_gazebo_hardware`, a plugin that
only honors effort (torque) commands. With `gazebo_effort:=false` the xacro
selects `gz_ros2_control/GazeboSimSystem`, which supports position and velocity.

**Decision**
Do not edit upstream. Write my own launch file in `workcell_bringup` that
reuses Franka's xacro with `gazebo_effort:=false`.

**Next**
Create `workcell_bringup/launch/` file, repeat the trajectory test, and confirm
joint 4 really reaches -2.056 rad.

**Lessons**
- Never trust "SUCCEEDED" alone. Verify with `/joint_states`.
- Always print joint names together with positions.
