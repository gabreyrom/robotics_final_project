# Robotics Final Project — ITAM

Autonomous perception, state estimation, and control for a simulated self-driving car, built for the *Robotics* course at ITAM using ROS Kinetic and the AutoNOMOS Gazebo simulator.

**Team "Trifuerza & Ganondorf"**
- Diego Amaya — 149119
- Gabriel Reynoso — 150904
- Gumer Rodríguez — 149109
- Julio Sánchez — 148221

## Overview

This repository implements the three parts of the final project:

| Part | File | Description |
|------|------|-------------|
| 1 — Self state estimation | [`histograma.cpp`](histograma.cpp) | Histogram filter that estimates the car's lane position from lane-marking point clouds, publishing a probability distribution over 8 discrete states. |
| 2 — Moving obstacle estimation | [`kalman.cpp`](kalman.cpp) | Kalman filter that fuses LiDAR range readings to estimate the position and velocity of a moving obstacle (the lead car). |
| 3 — Obstacle tracking | [`seguimiento.cpp`](seguimiento.cpp) | "Move to point" control strategy that steers and drives the car to follow the estimated obstacle position. |

A full write-up of the methodology and results is available in [`Proyecto3.pdf`](Proyecto3.pdf) (LaTeX sources under [`Proyecto3Escrito/`](Proyecto3Escrito)).

**Demo video:** https://drive.google.com/open?id=1wyU1Z710jF6q_1p19PAoiZ1Q8jpiKJ3T

## Prerequisites

- ROS Kinetic
- The [AutoNOMOS_simulation](https://github.com/EagleKnights/SDI-11911/wiki) Gazebo package, downloaded and working (`EK_AutoNOMOS`) — see the linked wiki for setup instructions.
- The sample bag file [`rosbag_SDI11911.bag`](http://robotica.itam.mx/rosbags/rosbag_SDI11911.bag), placed inside your ROS workspace.

## Setup

Clone this repository into the `src` folder of your ROS workspace:

```bash
git clone https://github.com/gabreyrom/proyecto_final.git
```

From the root of the workspace, build and source it:

```bash
catkin_make
source devel/setup.bash
```

Before running any part of the project, start `roscore` in a separate terminal.

## Usage

### Part 1 — Histogram filter (self state estimation)

This part replays recorded sensor data from the bag file. Open two terminals from the workspace root.

Terminal 1 — play back the recorded data:
```bash
rosbag play rosbag.bag
```

Terminal 2 — run the estimator:
```bash
rosrun proyecto_final histograma
```

The most likely lane position, computed from the bag data, is printed to this terminal.

### Parts 2 & 3 — Kalman filter and tracking control

These parts run live in Gazebo. From the `AutoNOMOS_simulation` directory, launch the simulation:

```bash
roslaunch autonomos_gazebo straight_road.launch
```

Once Gazebo opens with both cars, run the estimator and the controller from the ROS workspace, each in its own terminal:

Terminal 1 — Kalman filter (obstacle state estimation):
```bash
rosrun proyecto_final kalman
```

Terminal 2 — tracking controller:
```bash
rosrun proyecto_final seguimiento
```

Manually drive the lead car by publishing to its control topics:

```bash
# Steering angle
rostopic pub /AutoNOMOS_mini_2/manual_control/steering /std_msgs/Float32 '{data: VALUE}'

# Velocity
rostopic pub /AutoNOMOS_mini_2/manual_control/velocity /std_msgs/Float32 '{data: VALUE}'
```

In the Gazebo window, the second car will follow the first as it moves.

## Project structure

```
.
├── histograma.cpp        # Part 1: histogram filter
├── kalman.cpp             # Part 2: Kalman filter
├── seguimiento.cpp        # Part 3: tracking controller
├── CMakeLists.txt         # catkin build configuration
├── package.xml            # ROS package metadata
├── Proyecto3.pdf          # Project report
└── Proyecto3Escrito/      # LaTeX sources for the report
```
