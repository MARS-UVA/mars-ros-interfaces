# Mars ROS Interfaces

ROS 2 interface packages used by the Mars rover software. This repository contains the messages and action definitions shared between teleoperation, autonomy, robot control, and the serial hardware layer.

## Packages

### `teleop_msgs`

Messages for representing human controller input and the rover's selected drive mode.

| Interface | Purpose |
| --- | --- |
| `teleop_msgs/msg/StickPosition` | Normalized `x` and `y` positions for a control stick. |
| `teleop_msgs/msg/GamepadState` | Gamepad buttons, trigger values, and left/right stick positions. Trigger values are normalized to `0.0` through `1.0`. |
| `teleop_msgs/msg/HumanInputState` | A complete human input state, including `GamepadState`, drive mode, autonomous stop, and emergency stop flags. |

Drive mode constants in `HumanInputState` are:

- `DRIVEMODE_TELEOP = 0`
- `DRIVEMODE_AUTONOMOUS = 1`
- `DRIVEMODE_ADVANCED_TELEOP = 2`

### `serial_msgs`

Messages exchanged with the rover's serial or hardware layer. This package depends on `teleop_msgs`.

| Interface | Purpose |
| --- | --- |
| `serial_msgs/msg/MotorCommands` | Eight unsigned 8-bit motor command values, initialized to `127`. |
| `serial_msgs/msg/CurrentBusVoltage` | Wheel, drum, and actuator currents plus main and auxiliary battery voltages. Current values are in amperes and voltage values are in volts. |
| `serial_msgs/msg/Position` | Calibrated front and back actuator positions. |
| `serial_msgs/msg/Temperature` | Temperatures for the four wheels and two drums, in degrees Celsius. |

### `autonomy_msgs`

Action interface for requesting an autonomous action by index:

```text
autonomy_msgs/action/AutonomousActions
```

- Goal: `int32 index`
- Result: `bool success`
- Feedback: `string status`

### `robot_control_msgs`

Messages for the rover's arm and drum control and its overall operating state.

| Interface | Purpose |
| --- | --- |
| `robot_control_msgs/msg/ArmDrumControl` | Front and back arm speeds and front and back drum speeds, each defaulting to `0.0`. |
| `robot_control_msgs/msg/ArmControlMode` | Control mode values for the front and back arms. |
| `robot_control_msgs/msg/RobotState` | The rover's current operating state. |

State constants in `RobotState` are:

- `TELEOP = 0`
- `DIG = 1`
- `DUMP = 2`
- `ESTOP = 3`

`state` defaults to `ESTOP`.

## Building

From the root of a sourced ROS 2 workspace:

```bash
colcon build --packages-select teleop_msgs serial_msgs autonomy_msgs robot_control_msgs
source install/setup.bash
```

The generated C++, Python, and ROS 2 CLI interfaces are then available to downstream packages.

## Using the interfaces

Example ROS 2 type names:

```bash
ros2 interface show teleop_msgs/msg/GamepadState
ros2 interface show serial_msgs/msg/MotorCommands
ros2 interface show autonomy_msgs/action/AutonomousActions
ros2 interface show robot_control_msgs/msg/RobotState
```

Include the relevant package as a dependency in a consuming package's `package.xml` and build configuration before importing or using its generated interfaces.
