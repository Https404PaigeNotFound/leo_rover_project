# leo_rover_project

## Prerequisites:
ROS 2 Humble ([source](https://docs.ros.org/en/humble/Installation/Alternatives/Ubuntu-Development-Setup.html) | [deb packages](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debs.html)) installed on Ubuntu 22.04.

### Setup 
Clone GitHub Repository to your chosen directory.
(In Ubuntu terminal)
```
cd ~
git clone https://github.com/Https404PaigeNotFound/leo_rover_project.git
cd leo_rover_project
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
Install Python Dependencies
```
pip install -r requirements.txt
```
Install ROS Dependencies
```
cd ~/leo_rover_project/ros2_ws
rosdep update
rosdep install --from-paths src --ignore-src -r -y
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

