# leo_rover_project

## Prerequisites:
ROS 2 Humble ([source](https://docs.ros.org/en/humble/Installation/Alternatives/Ubuntu-Development-Setup.html) | [deb packages](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debs.html)) installed on Ubuntu 22.04.

### Setup 
If setup from nothing, make github repo online and clone it to your chosen directory.
(In Ubuntu terminal)
```
cd ~
git clone https://github.com/YOUR_USERNAME/leo_rover_project.git
cd leo_rover_project
```

Initialise ROS 2 Workspace
```
mkdir -p ros2_ws/src
cd ros2_ws
```

Create Python Virtual Environment
```
cd ~/leo_rover_project
python3 -m venv venv
```
Activate Virtual Environment
```
source venv/bin/activate
```
Upgrade pip
```
pip install --upgrade pip
```

Create and Populate requirements.txt
```
cat <<EOL > requirements.txt
numpy
opencv-python
matplotlib
pillow
rclpy
PyYAML
tk
EOL
```


Install Python Dependencies
```
pip install -r requirements.txt
```

Navigate to Workspace Source Directory
```
cd ~/leo_rover_project/ros2_ws/src
```

Clone Necessary Repositories
```
git clone -b humble https://github.com/LeoRover/leo_simulator-ros2.git
git clone https://github.com/LeoRover/leo_common-ros2.git
```


Install ROS Dependencies
```
cd ~/leo_rover_project/ros2_ws
rosdep update
rosdep install --from-paths src --ignore-src -r -y
```

Ensure your system supports the required OpenGL version.
```
glxinfo | grep "OpenGL version"
```

If `glxinfo` is not found, install it.
```
sudo apt install mesa-utils
```

Build the Workspace. 
```
colcon build --symlink-install
```
_If you encounter errors during the build process, such as issues with symbolic links, ensure that the build/, install/, and log/ directories are clean._
```
cd ~/leo_rover_project/ros2_ws
rm -rf build/ install/ log/
```
Then try to `colcon build --symlink-install` again. The `--symlink-install flag` allows for faster development cycles by avoiding the need to rebuild the workspace after every change. If issues with building the workspace occur then remove the `--symlink-install flag`.  
  
Source the Workspace
```
source install/setup.bash
```
_Optional: To ensure the workspace is sourced in every new terminal session_
```
echo "source ~/leo_rover_project/ros2_ws/install/setup.bash" >> ~/.bashrc
source ~/.bashrc
```
Configure Environment Variables. To prevent issues related to Gazebo environment variables
```
export GZ_SIM_SYSTEM_PLUGIN_PATH=$GZ_SIM_SYSTEM_PLUGIN_PATH:/opt/ros/humble/lib
export GZ_SIM_RESOURCE_PATH=$GZ_SIM_RESOURCE_PATH:/opt/ros/humble/share:/opt/ros/humble/share/leo_gz_worlds/worlds:/opt/ros/humble/share/leo_gz_worlds/models
```

### Launch the Leo Rover Simulation
Change directory and ensure to activate the python virtual environment
```
cd ~/leo_rover_project
source venv/bin/activate
```

Source the ROS 2 and Workspace Setup Scripts
```
source /opt/ros/humble/setup.bash
source ~/leo_rover_project/ros2_ws/install/setup.bash
```
Launch the Simulation
```
ros2 launch leo_gz_bringup leo_gz.launch.py
```
_If you encounter issues related to OpenGL or graphics rendering, you can force the use of software rendering. Add this line to your ~/.bashrc file to make it permanent._
```
export LIBGL_ALWAYS_SOFTWARE=1
```

### Add RPLIDAR, RealSense, and Interbotix Arm to Simulation 
```
cd ~/leo_rover_project/ros2_ws/src
git clone https://github.com/robopeak/rplidar_ros.git
git clone https://github.com/IntelRealSense/realsense-ros.git
colcon build
source install/setup.bash
```

### Add PincherX 150 Manipulator Arm

If you have a native linux install then you can follow the instrutions below from [Interbotix X-Series Manipulators Documentation ROS 2 Standard Software Setup Guide.](https://docs.trossenrobotics.com/interbotix_xsarms_docs/ros_interface/ros2/software_setup.html)
```
sudo apt install curl
curl 'https://raw.githubusercontent.com/Interbotix/interbotix_ros_manipulators/main/interbotix_ros_xsarms/install/amd64/xsarm_amd64_install.sh' > xsarm_amd64_install.sh
chmod +x xsarm_amd64_install.sh
./xsarm_amd64_install.sh -d humble
```

#### If you're running linux in a vm then can use the below
If your python virutal enviroment is active, deactivate it.
```
deactivate
```
clone repo
```
cd ~/leo_rover_project/ros2_ws/src
git clone -b humble https://github.com/Interbotix/interbotix_ros_core.git
git clone -b humble https://github.com/Interbotix/interbotix_ros_toolboxes.git
git clone -b humble https://github.com/Interbotix/interbotix_ros_manipulators.git
git clone https://github.com/Interbotix/interbotix_xs_driver.git

```
install
```
sudo apt update
sudo apt install ros-humble-rosidl-typesupport-c
```

_If required_ install necessary dependencies
```
sudo apt install ros-humble-ament-cmake-nose
```

_If required_ ensure CMake path points to the ROS 2 installation
```
export CMAKE_PREFIX_PATH=$CMAKE_PREFIX_PATH:/opt/ros/humble
```

remove file
```
cd ~/leo_rover_project/ros2_ws/src/interbotix_ros_core/interbotix_ros_xseries/interbotix_xs_msgs

rm COLCON_IGNORE

```

Install required dependencies
```
cd ~/leo_rover_project/ros2_ws
rosdep install --from-paths src --ignore-src -r -y
cd ~/leo_rover_project/ros2_ws
source /opt/ros/humble/setup.bash
colcon build
source install/setup.bash
```

Verify the installation
```
ros2 pkg list | grep interbotix
```
Let's lanch something but first we active virtul env and source to update the enviroment

```
cd ~/leo_rover_project
source venv/bin/activate
source install/setup.bash

```


Test by launching 
```
ros2 launch interbotix_xsarm_descriptions xsarm_description.launch.py robot_model:=px150 use_joint_pub_gui:=true
```



_if needed_ clean workspace then build
```
cd ~/leo_rover_project/ros2_ws
rm -rf build/ install/ log/
```

alos if issues building, build the below first
```
colcon build --packages-select interbotix_xs_msgs
```

_other considerations_ ensure that the udev rules are configured correctly so that the U2D2 device is recognized as /dev/ttyDXL. You can verify this by running
```
ls /dev | grep ttyDXL

```
If ttyDXL is not listed, you may need to set up the udev rules as outlined in the [Interbotix X-Series Manipulators Documentation ROS 2 Standard Software Setup Guide.](https://docs.trossenrobotics.com/interbotix_xsarms_docs/ros_interface/ros2/software_setup.html)