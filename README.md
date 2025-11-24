# 🛸 Drone Teleoperation Using KeyBoard/ Joystick (ROS 2 + PX4)

Control a **simulated drone** using **keyboard/ joystick commands** via **ROS 2 Humble** and **PX4 Autopilot**.
Visualize in **Gazebo**, monitor in **QGroundControl**, and bridge communication using the **Micro XRCE-DDS Agent**.

---

## 🎯 Objective

Create a ROS 2-based teleoperation setup for a PX4 SITL drone:

* Run drone simulation in Gazebo Sim
* Monitor with QGroundControl
* Control it with your keyboard/ joystick via ROS 2 topics

---

## ⚙️ System Requirements

* 🐧 Ubuntu 22.04 LTS
* 🌀 ROS 2 Humble Hawksbill
* 💾 8–10 GB free space
* 💡 Basic terminal knowledge

---

# 🚀 Quick Start — How to Use This Repository

### **1️⃣ Create Your ROS 2 Workspace**

```bash
cd ~
mkdir -p px4_ros2_ws/src
cd px4_ros2_ws/src
```

---

### **2️⃣ Clone the Repository with Submodules**

```bash
git clone https://github.com/MechaMind-Labs/ROS2-PX4_Drone_Teleoperation_Using_Joystick.git --recursive
```

---

### **3️⃣ Install Dependencies**

Use `rosdep` to automatically install all required ROS 2 dependencies:

```bash
cd ~/px4_ros2_ws
sudo apt install python3-rosdep -y
sudo rosdep init
rosdep update
rosdep install --from-paths src --ignore-src -r -y
```

---

### **4️⃣ Build the Workspace**

```bash
colcon build
```

---

### **5️⃣ Source the Workspace**

```bash
source install/setup.bash
```

---

### **6️⃣ Launch the Simulation + PX4 + XRCE Agent**

In **Terminal 1**, start the full PX4 bringup:

```bash
ros2 launch px_bringup minimal.launch.py
```

✔️ This launches:

* PX4 SITL
* Gazebo Sim with drone
* Micro XRCE-DDS Agent
* PX4–ROS2 communication bridge

A drone will appear in Gazebo. 🛸

---

### **7️⃣ Run Teleoperation (Keyboard or Joystick)**

Open **Terminal 2**, choose **either keyboard or joystick teleoperation**:

#### 🖮 Keyboard Teleoperation

```bash
ros2 launch px4_offboard keyboard_teleop.launch.py
```

#### 🎮 Joystick Teleoperation

```bash
ros2 launch px4_offboard joystick_teleop.launch.py
```

You can now control the drone in Gazebo! 🚀🕹️

---

## 🧩 Setup Guide

> 💡 Open three terminals — one each for **PX4 SITL**, **XRCE Agent**, and **QGroundControl**.

---

### 1️⃣ Install ROS 2 Humble

```bash
sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

sudo apt install software-properties-common
sudo add-apt-repository universe
sudo apt update && sudo apt install curl -y

sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
  -o /usr/share/keyrings/ros-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] \
http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | \
sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null

sudo apt update && sudo apt upgrade -y
sudo apt install -y ros-humble-desktop ros-dev-tools

source /opt/ros/humble/setup.bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
```

Install required Python dependencies:

```bash
pip install --user -U empy==3.3.4 pyros-genmsg setuptools
```

---

### 2️⃣ Install PX4 Autopilot (Simulation)

```bash
cd
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
bash ./PX4-Autopilot/Tools/setup/ubuntu.sh
cd PX4-Autopilot/
make px4_sitl
```

#### Test the SITL simulation (Terminal 1)

```bash
make px4_sitl gz_x500
```

✅ Gazebo Sim should launch with an x500 drone model.

---

### 3️⃣ Install and Run Micro XRCE-DDS Agent (Terminal 2)

```bash
git clone https://github.com/eProsima/Micro-XRCE-DDS-Agent.git
cd Micro-XRCE-DDS-Agent
mkdir build && cd build
cmake ..
make
sudo make install
sudo ldconfig /usr/local/lib/
```

Run the agent:

```bash
MicroXRCEAgent udp4 -p 8888
```

Expected:

```
UDP agent listening on port 8888
```

---

### 4️⃣ Run QGroundControl (Terminal 3)

1. Download the AppImage:
   👉 [QGroundControl Downloads](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html)

2. Run it:

```bash
chmod +x ~/Downloads/QGroundControl-x86_64.AppImage
cd ~/Downloads
./QGroundControl-x86_64.AppImage
```

QGroundControl connects automatically when PX4 SITL is running.

---

### 5️⃣ ROS 2 Integration with PX4

Create your workspace:

```bash
cd ~
mkdir -p px4_ros2_ws/src
cd px4_ros2_ws/src
```

Clone PX4 ROS 2 packages:

```bash
git clone https://github.com/PX4/px4_msgs.git
git clone https://github.com/PX4/px4_ros_com.git
```

Build and source:

```bash
cd ..
source /opt/ros/humble/setup.bash
colcon build
source install/local_setup.bash
```

Test ROS 2 ↔ PX4 communication:

```bash
ros2 launch px4_ros_com sensor_combined_listener.launch.py
```

If successful, you’ll see real-time IMU/sensor updates in the console.

---

### 6️⃣ Manual PX4 Commands (Quick Check)

Run these in the PX4 terminal:

```bash
commander arm       # Arm motors
commander takeoff   # Take off vertically
commander land      # Land the drone
commander disarm    # Disarm after landing
```

---

## 🔍 Verify ROS 2 ↔ PX4 Bridge

```bash
source /opt/ros/humble/setup.bash
ros2 topic list | grep fmu
```

Expected output:

```
/fmu/vehicle_status/out
/fmu/sensor_combined/out
/fmu/vehicle_command/in
```

✅ ROS 2 is now receiving PX4 data successfully.

---

## 🧰 Troubleshooting

| Issue                          | Solution                                   |
| ------------------------------ | ------------------------------------------ |
| QGroundControl fails to launch | Install `libfuse2` and `gstreamer` plugins |
| XRCE Agent port busy           | Ensure no other agent process is running   |
| No ROS topics                  | Confirm PX4 and XRCE Agent are running     |

---

## 📚 References

* [PX4 Autopilot Docs](https://docs.px4.io/main/en/dev_setup/dev_env_linux_ubuntu)
* [PX4 ROS 2 Interface (px4_ros_com)](https://github.com/PX4/px4_ros_com)
* [Micro XRCE-DDS Agent Docs](https://github.com/eProsima/Micro-XRCE-DDS-Agent)
* [QGroundControl Guide](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html)
* [ROS2 Humble Docs](https://docs.ros.org/en/humble/index.html)

---

## 🙌 Credits

Maintained by **[Curious-Utkarsh](https://github.com/Curious-Utkarsh)**
Inspired by real-world **drone teleoperation robotic** applications.

---