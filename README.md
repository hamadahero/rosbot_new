🦾 rosbot_new

A ROS2-based autonomous robot simulation project that integrates Gazebo, SLAM, and Navigation using use_sim_time for synchronized operation.
This setup allows you to visualize and control the robot in a simulated world while building real-time maps.

📘 Overview

rosbot_new provides a full pipeline for robot simulation, mapping, and navigation within Gazebo using ROS2.
It is structured to support:

Simulated environments

Real-time mapping with SLAM Toolbox

Navigation stack integration

Velocity command relay for differential drive

⚙️ Features

🌍 Gazebo simulation world support

🗺️ SLAM Toolbox for online mapping

🚘 Navigation stack integration

🔁 /cmd_vel → /diff_cont/cmd_vel_unstamped topic relay

🧩 Fully modular ROS2 package

🧰 Prerequisites
Install dependencies:

sudo apt update
sudo apt install ros-humble-desktop-full \
                 ros-humble-slam-toolbox \
                 ros-humble-nav2-bringup \
                 ros-humble-topic-tools \
                 ros-humble-gazebo-ros-pkgs

🏗️ Installation:

# Clone the repository
git clone https://github.com/hamadahero/rosbot_new.git
cd rosbot_new

# Build your workspace
colcon build --symlink-install

# Source setup file
source install/setup.bash

🚀 Running the Simulation

Follow these steps in order to start your simulation, mapping, and navigation process:

🔹 Step 1 – Launch Gazebo Simulation

📝 Replace <your_absolute_path> with the full path to your cones_course.world file.

ros2 launch rosbot gazebo_sim.launch.py world:=<your_absolute_path>/cones_course.world use_sim_time:=true

Example:
ros2 launch rosbot gazebo_sim.launch.py world:=/home/rahul/rosbot_new/src/rosbot/worlds/cones_course.world use_sim_time:=true

🔹 Step 2 – Open RViz2 for Visualization

📝 Replace <your_absolute_path> with the full path to your newrosbot.rviz file.
rviz2 -d <your_absolute_path>/newrosbot.rviz

Example:
rviz2 -d /home/rahul/rosbot_new/src/rosbot/config/newrosbot.rviz

🔹 Step 3 – Run SLAM (Online Async Mapping)

📝 Replace <your_absolute_path> with the full path to your mapper_params_online_async.yaml file.

ros2 launch rosbot online_async_launch.py slam_params_file:=<your_absolute_path>/mapper_params_online_async.yaml use_sim_time:=true

Example:
ros2 launch rosbot online_async_launch.py slam_params_file:=/home/rahul/rosbot_new/src/rosbot/config/mapper_params_online_async.yaml use_sim_time:=true

⚠️ Important Configuration Step:

Inside your file
src/rosbot/config/mapper_params_online_async.yaml,
find the line that defines:

map_file_name: <your_path>/new_map_serialize
and replace <your_path> with the absolute path to your maps folder.

Example:
map_file_name: /home/rahul/rosbot_new/src/rosbot/maps/new_map_serialize
This ensures your generated SLAM map is saved and serialized correctly.

🔹 Step 4 – Launch Navigation

After mapping begins, launch navigation in a new terminal:
ros2 launch rosbot navigation_launch.py use_sim_time:=true

🗂️ Folder Structure
rosbot_new/
├── src/
│   └── rosbot/
│       ├── launch/
│       │   ├── gazebo_sim.launch.py
│       │   ├── online_async_launch.py
│       │   └── navigation_launch.py
│       ├── worlds/
│       │   └── cones_course.world
│       ├── config/
│       │   ├── mapper_params_online_async.yaml
│       │   ├── newrosbot.rviz
│       │   └── other_configs.yaml
│       ├── maps/
│       │   └── new_map_serialize
│       ├── urdf/
│       └── ...
├── build/
├── install/
└── README.md

💡 Tips

Always use use_sim_time:=true for synchronization across Gazebo, SLAM, and Navigation.

Use ros2 topic list and ros2 node list to confirm running topics/nodes.

Make sure your YAML and world paths are absolute, not relative.

You can modify the Gazebo .world file to create new obstacle courses or environments.


🤝 Contributing

Pull requests are welcome!

Fork the repository

Create a new branch (git checkout -b feature-name)

Commit changes and open a PR with a clear explanation
