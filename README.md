# hackathon2024

## Parts
Jetson nano
- 64GB SD-card
- wifi USB-dongle
- l4t r32.7.4

## Installing ROS2

### Dustynv
https://developer.nvidia.com/blog/implementing-robotics-applications-with-ros-2-and-ai-on-jetson-platform-2/
Clone https://github.com/dusty-nv/jetson-containers. Do the basic system setup (mainly mount external SSD for docker data) and use it to build:
`./build.sh --name=my_ros ros:humble-base realsense`.

https://forums.developer.nvidia.com/t/ros2-on-jetson-nano/174815
But, since building takes long and fails easily, we can also try to go with foxy instead: `./run.sh $(./autotag dustynv/realsense:r32.7.1 dustynv/ros:foxy-ros-base-l4t-r32.7.1)`. 

Oh, but to get a combined image we have to build it. So let's try `./build.sh --name=my_ros ros:foxy-ros-base realsense`.

Crucial for fixing the realsense build: https://github.com/dusty-nv/jetson-containers/issues/281.

However, when running the container it seems impossible to get the Intel® RealSense™ ROS2 wrapper.

Try again, now with pre-built image: `./run.sh $(./autotag  dustynv/ros:humble-ros-base-l4t-r32.7.1)`. Follow instructions to build from: https://github.com/IntelRealSense/librealsense/blob/master/doc/installation.md. Problem is driver build fails because of incompatible kernel version (4.9 vs. 4.15).

Failure with errors.

### ROS2 foxy on 18.04
Given the issues experienced with the dustynv docker images, let's try a direct install of Foxy on the Jetson nano
- Get and flash latest SD-card image for Jetson nano (l4t-r32.7.4).
- Update and upgrade.
- Clone https://github.com/jetsonhacks/installROS2.git and execute `./installROS2.sh`.
- Next install realsense driver and ROS package.

Again failure with errors.

### Linorobot2
- https://github.com/linorobot/linorobot2
- https://github.com/linorobot/ros2me
- https://github.com/IntelRealSense/librealsense/blob/master/doc/installation_jetson.md
- 

## Optional

### Python
To install a more recent version of Python: https://computingforgeeks.com/how-to-install-python-on-ubuntu-linux-system/.

### LsLiDAR N10P
- https://github.com/Lslidar/Lslidar_ROS2_driver.git - original
- https://github.com/helloworlder8/n10.git - has serial launch examples
- https://github.com/quebeh/n10.git - experiment getting serial data using Python script
- https://gitee.com/astraLX/lsn10 - example with documentation (red: 5V, black: GND, green: RX, yellow: TX - 3V3 uart TTL)

![image](https://github.com/jabratn/hackathon2024/assets/10284201/22f27486-87b4-42c7-8efc-5c80700d7217)

![image](https://github.com/jabratn/hackathon2024/assets/10284201/02a73f8e-2a8e-4985-b729-fcb20473b32d)

## Also interesting

### Tutorials
- http://www.yahboom.net/study/ROSMASTER-X3

### Yocto
- https://news.accelerationrobotics.com/ros-2-humble-in-nvidia-jetson-nano-with-yocto/

Install and build is looking good, but eventually breaks with:

```| bin/cmake: /home/bartjan/Sandbox/yocto/tegra-bsp-honister/build/tmp/sysroots-uninative/x86_64-linux/usr/lib/libstdc++.so.6: version `GLIBCXX_3.4.30' not found (required by bin/cmake)```

Possible solution is to try to change GCC version in bitbake recipies (for native and cross build) from 11.2.0 to 11.4.0 (used in Linux Mint of build machine).

### Ubuntu 20.04 on Jetson nano
- https://qengineering.eu/install-ubuntu-20.04-on-jetson-nano.html- https://github.com/Qengineering/Jetson-Nano-Ubuntu-20-image

### Minimal Jetson image
- https://github.com/pythops/jetson-image

### other docker
- https://github.com/2b-t/realsense-ros2-docker

### other robot
- https://www.enthusiasticroboticist.com/blog/ros-2-on-jetson-nano-using-docker/
