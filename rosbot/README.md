# 🤖 ROSbot Simulation with Nav2 (ROS 2 Humble)
<p align="center">
<img src="https://www.google.com/search?q=https://placehold.co/800x400/1e293b/ffffff%3Ftext%3DROSbot%2BGazebo%2BSimulation" alt="ROSbot Gazebo Simulation Banner"/>
</p>

<p align="center">
<a href="#-prerequisites">Prerequisites</a> •
<a href="#-getting-started">Getting Started</a> •
<a href="#️-running-the-simulation">Running the Simulation</a> •
<a href="#-optional-running-amcl-for-localization">AMCL Localization</a> •
<a href="#-troubleshooting">Troubleshooting</a>
</p>

Welcome! This repository provides a complete simulation environment for the ROSbot mobile robot using the Navigation2 (Nav2) stack on the ROS 2 Humble Hawksbill distribution.

This branch is fully configured for the Humble release, allowing you to seamlessly perform mapping with SLAM Toolbox and autonomous navigation within a Gazebo simulation.

## ✨ Features
ROS 2 Humble Ready: Fully compatible with the latest LTS version of ROS 2.

Gazebo Simulation: Includes a pre-configured Gazebo world (cones_course.world).

Full Nav2 Stack: Launches all necessary nodes for autonomous navigation, including path planning, obstacle avoidance, and recovery behaviors.

Real-time SLAM: Utilizes the SLAM Toolbox for creating a map of the environment on the fly.

Localization Ready: Includes a separate launch file to use the AMCL package for localization within a pre-existing map.

RViz Configuration: A pre-made RViz2 setup is provided for easy visualization of the robot's state, sensor data, and navigation progress.

## 📋 Prerequisites
Before you begin, ensure your system (Ubuntu 22.04 is recommended) has the following installed:

ROS 2 Humble Hawksbill: Follow the Official Installation Guide. The ros-humble-desktop-full installation is highly recommended as it includes Gazebo.

Colcon Build Tool: 
```bash

sudo apt install python3-colcon-common-extensions
```
ROS 2 Dependencies: Install the Nav2 and SLAM Toolbox packages.
```bash

sudo apt update
sudo apt install ros-humble-navigation2 ros-humble-nav2-bringup ros-humble-slam-toolbox
```
🚀 Getting Started
Follow these steps to set up your environment and run the simulation.

1. Create a ROS 2 Workspace
If you don't have one already, create a new ROS 2 workspace.
```bash
mkdir -p ~/rosbot_ws/src
cd ~/rosbot_ws
```
2. Clone the Repository
Clone this repository into the src directory of your workspace.

cd ~/rosbot_ws/src
# Replace the URL with your repository's actual clone URL
git clone [https://github.com/your-username/your-rosbot-repo.git](https://github.com/your-username/your-rosbot-repo.git) rosbot

3. Install Dependencies and Build
Navigate to your workspace root, use rosdep to install any missing dependencies, and then build the packages with colcon.
```bash

cd ~/rosbot_ws
rosdep install -i --from-path src --rosdistro humble -y
colcon build
```
4. Configure the SLAM Parameters
You need to make a small but important change to the SLAM configuration file. This prevents errors related to hardcoded file paths.

Open the file: ~/rosbot_ws/src/rosbot/config/mapper_params_online_async.yaml

Find the parameter: map_file_name

Modify its value: Change the value from a full path (e.g., /home/your_username/my_map) to a relative name (e.g., my_map). This allows the map to be saved in the directory where you run the command.

Before:

## ... other parameters
map_file_name: /home/someuser/maps/my_map
# ...

After:

## ... other parameters
map_file_name: my_map 
# ...

Save the file after making the change.

▶️ Running the Simulation
To launch the full simulation with SLAM and Nav2, you will need to open 5 separate terminals.

Important: In each new terminal, you must source your workspace's setup file first!

# Run this in each new terminal
cd ~/rosbot_ws/
source install/setup.bash

➡️ Terminal 1: Launch Gazebo
This command starts the Gazebo simulator, loads the specified world, and spawns the ROSbot model.
```bash

ros2 launch rosbot gazebo_sim.launch.py world:=./src/rosbot/worlds/cones_course.world use_sim_time:=true 
```
➡️ Terminal 2: Launch RViz
This command opens RViz2 with a pre-configured layout to visualize the robot's sensor data, the map being built, costmaps, and the planned path.
```bash

rviz2 -d ./src/rosbot/config/newrosbot.rviz
```
➡️ Terminal 3: Run SLAM Toolbox
This launches the SLAM Toolbox node, which processes laser scan data to build a map of the environment in real-time.
```bash

ros2 launch rosbot online_async_launch.py slam_params_file:=./src/rosbot/config/mapper_params_online_async.yaml use_sim_time:=true
```
➡️ Terminal 4: Run Nav2
This command brings up the entire Navigation2 stack, including the planner, controller, and behavior server.
```bash

ros2 launch rosbot navigation_launch.py use_sim_time:=true
```
➡️ Terminal 5: Remap cmd_vel Topic
This final command acts as a bridge, relaying the velocity commands from Nav2 (/cmd_vel) to the topic the Gazebo robot controller expects (/diff_cont/cmd_vel_unstamped).
```bash

ros2 run topic_tools relay /cmd_vel /diff_cont/cmd_vel_unstamped 
```
🎉 Congratulations! The simulation is now fully operational. You can now use the "Nav2 Goal" tool in RViz to set a destination and watch the ROSbot navigate autonomously.

📍 Optional: Running AMCL for Localization
If you have already created and saved a map, you can run pure localization using the AMCL package instead of building a new map with SLAM.

To do this, simply replace the commands for Terminal 3 and Terminal 4 with the following:

DO NOT RUN the online_async_launch.py command from Terminal 3.

In Terminal 4, use this command instead of navigation_launch.py:
```bash

ros2 launch rosbot localisation_launch.py use_sim_time:=true
```
Note: This assumes you have a map file (map.yaml) available that the localisation_launch.py file is configured to use.

## 🐛 Troubleshooting
Commands not found? Make sure you have sourced your workspace in every terminal: source ~/rosbot_ws/install/setup.bash.

Packages not found during build? Ensure you have installed all dependencies with rosdep install and that all required ROS 2 packages (Nav2, SLAM Toolbox) are installed correctly.

Robot not moving? Double-check that the topic_tools relay command in Terminal 5 is running correctly and that there are no errors in the Nav2 terminal (Terminal 4).

## 🤝 Contributing
Contributions are welcome! If you have suggestions for improvements, please open an issue or submit a pull request.

## 📄 License
This project is licensed under the MIT License.