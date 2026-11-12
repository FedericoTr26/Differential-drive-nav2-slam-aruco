# Differential-Drive Robot Navigation with Nav2, SLAM and ArUco

A ROS 2 and Gazebo Sim project that uses Nav2 to navigate a differential-drive FRA2MO robot, map its environment, and detect an ArUco marker during an approach-and-return mission.

[Technical report](Trenti_hw4.pdf)

## What we built

- Configured a Gazebo course, robot spawn pose, and named waypoint sequences for navigation and mapping.
- Used Nav2 Simple Commander to send waypoints and SLAM Toolbox to build an occupancy map.
- Compared SLAM and costmap settings for map completion and traversal time.
- Integrated a simulated camera and ArUco detection to publish the pose of marker 115 in the map frame.

## Run the demos

Use ROS 2 Humble with Gazebo Sim, the ROS–Gazebo bridge, Nav2, SLAM Toolbox, and RViz 2. Install `rqt_image_view` to inspect the marker detector output. The ArUco ROS packages are included in this repository.

From the `src` directory of your ROS 2 workspace, clone the repository.

```bash
git clone https://github.com/FedericoTr26/Differential-drive-nav2-slam-aruco.git
```

Return to the workspace root to build and source the workspace.

```bash
colcon build
source install/setup.bash
```

Source `install/setup.bash` from the workspace root in each new terminal.

Start Gazebo before running each demo. Press **Play** if the simulation is paused.

```bash
ros2 launch rl_fra2mo_description gazebo_fra2mo.launch.py
```

To view the robot and TF frames in a separate terminal, open the RViz robot view.

```bash
ros2 launch rl_fra2mo_description display_fra2mo.launch.py
```

### Waypoint navigation and mapping

In a second terminal, start Nav2 and SLAM.

```bash
ros2 launch rl_fra2mo_description fra2mo_explore.launch.py
```

In a third terminal, use the first command for the four-waypoint navigation demo or the second command for seven-waypoint mapping.

```bash
ros2 run rl_fra2mo_description follow_waypoints.py
ros2 run rl_fra2mo_description follow_waypoints.py --ros-args -p target:="map"
```

For the mapping view, open RViz in another terminal. After completing the mapping mission, run the map saver from the directory where you want to write the map.

```bash
ros2 launch rl_fra2mo_description display_fra2mo.launch.py map:=true
```

```bash
ros2 run nav2_map_server map_saver_cli -f map
```

### ArUco approach and return

With Gazebo running, start the combined navigation and detection launch. It brings up the navigation stack, sends the approach and return waypoints, and runs the marker detector.

```bash
ros2 launch rl_fra2mo_description vision_navigation.launch.py
```

Open the RViz robot view to inspect the frames, or use the topic echo to see the published transform. In the image viewer, select `/aruco_single/result` to see the annotated camera feed.

```bash
ros2 topic echo /tf_static
```

```bash
ros2 run rqt_image_view rqt_image_view
```

## Results and recordings

| Demo | Result | Recording |
| --- | --- | --- |
| Four-waypoint navigation | Visits the four goals in the order 3, 4, 2, 1. | [Waypoint navigation](Videos/video01_t7Ft1NfO.mp4) |
| Seven-waypoint mapping | Builds an occupancy map as the robot traverses the course. | [SLAM mapping](Videos/video02_pzuhOnNf.mp4) |
| ArUco approach and return | Detects marker 115, publishes its pose in the map frame, and returns to the starting area. | [ArUco mission](Videos/video03.mp4) |

The report records a mapping time of **6.11 min** in the baseline trial and **3.90 min** in the best reported trial. The best trial used a map resolution of `0.065` m, a minimum travel distance of `0.1` m, and a minimum travel heading of `0.05` rad. The [committed SLAM configuration](rl_fra2mo_description/config/slam.yaml) uses `0.05`, `0.01`, and `0.01` for those three settings.

The repository includes a [saved occupancy map](rl_fra2mo_description/maps/map.pgm) and its [YAML metadata](rl_fra2mo_description/maps/map.yaml).

## Attribution

Developed as a group project for Robotics Lab using the course FRA2MO simulation package. The bundled [ArUco ROS](aruco_ros/) and [m-explore ROS 2](m-explore-ros2/) packages retain their upstream documentation and licenses.
