# hackathon2024

## Parts
Jetson nano
- 64GB SD-card
- wifi USB-dongle
- l4t r32.7.4

## Installing ROS2
https://developer.nvidia.com/blog/implementing-robotics-applications-with-ros-2-and-ai-on-jetson-platform-2/
Clone https://github.com/dusty-nv/jetson-containers. Do the basic system setup (mainly mount external SSD for docker data) and use it to build:
`./build.sh --name=my_ros ros:humble-base realsense`.

https://forums.developer.nvidia.com/t/ros2-on-jetson-nano/174815
But, since building takes long and fails easily, we can also try to go with foxy instead: `./run.sh $(./autotag dustynv/realsense:r32.7.1 dustynv/ros:foxy-ros-base-l4t-r32.7.1)`. 

Oh, but to get a combined image we have to build it. So let's try `./build.sh --name=my_ros ros:foxy-ros-base realsense`.

Crucial for fixing the realsense build: https://github.com/dusty-nv/jetson-containers/issues/281.

## Optional

### Python
To install a more recent version of Python: https://computingforgeeks.com/how-to-install-python-on-ubuntu-linux-system/.

### LsLiDAR N10P
- https://github.com/Lslidar/Lslidar_ROS2_driver.git - original
- https://github.com/helloworlder8/n10.git - has serial launch examples
- https://github.com/quebeh/n10.git - experiment getting serial data using Python script
- https://gitee.com/astraLX/lsn10 - example with documentation (red: 5V, black: GND, green: RX, yellow: TX - 3V3 uart TTL)

![image](https://github.com/jabratn/hackathon2024/assets/10284201/85251435-99b7-4ee1-a86c-9e1f8b3e9ada)
![image](https://github.com/jabratn/hackathon2024/assets/10284201/2fb1b9fe-7e04-4732-b315-403e12aa9a4a)

## Also interesting

### Tutorials
- http://www.yahboom.net/study/ROSMASTER-X3

### Yocto
- https://news.accelerationrobotics.com/ros-2-humble-in-nvidia-jetson-nano-with-yocto/

### Ubuntu 20.04 on Jetson nano
- https://qengineering.eu/install-ubuntu-20.04-on-jetson-nano.html- https://github.com/Qengineering/Jetson-Nano-Ubuntu-20-image

### Minimal Jetson image
- https://github.com/pythops/jetson-image

### other docker
- https://github.com/2b-t/realsense-ros2-docker
