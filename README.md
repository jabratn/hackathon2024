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

Eloquent not supported by linorobot2.

### Jetbot-ros2
Next attempt, following: https://github.com/jdgalviss/jetbot-ros2
- Jetson nano (l4t-r32.7.4)
- Install ros-eloquent packages
- Install librealsense2 packages
- Clone the various git repos.
- Skip the Teleoperation support

Rtabmap - no longer supports eloquent

### Jetson Xavier NX and NVidia Isaac
- https://nvidia-isaac-ros.github.io/index.html

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

However, when building on WSL2/Ubuntu-22.04 it seems to go somewhat better. But now failed with:

```
Cloning into bare repository '/home/bartj/build/tegra-bsp-honister/build/downloads/git2/github.com.git.OE4T.linux-tegra-4.9'...
remote: Not Found
fatal: repository 'https://github.com/git/OE4T/linux-tegra-4.9/' not found
DEBUG: Mirror fetch failure for url git://github.com/git/OE4T/linux-tegra-4.9;protocol=https;name=machine;branch=oe4t-patches-l4t-r32.6 (original url: git://github.com/OE4T/linux-tegra-4.9;protocol=https;name=machine;branch=oe4t-patches-l4t-r32.6)
```

Aha: URL should probably be: https://github.com/OE4T/linux-tegra-4.9/. Let's try to fix this.

### Buildroot
- https://github.com/celaxodon/buildroot/tree/feat/jetson-nano-support-latest/board/nvidia/jetson_nano

With this latest branch, building seems to be working, for now. 

Fix host-m4 build by splitting line in `./output/build/host-m4-1.4.18/lib/c-stack.c`:
```
#elif HAVE_LIBSIGSEGV      
# if  (SIGSTKSZ < 16384)
```

Building Linux_for_Tegra fails with:

```
make[2]: *** No rule to make target 'arch/arm64/boot/dts/..//hardware/nvidia/platform/t210/porg/kernel-dts/tegra210-p3448-0000-p3449-0000-a00.dts', needed by 'arch/arm64/boot/dts/_ddot_//hardware/nvidia/platform/t210/porg/kernel-dts/tegra210-p3448-0000-p3449-0000-a00.dtb'.  Stop.
make[2]: *** Waiting for unfinished jobs....
  DTC     arch/arm64/boot/dts/_ddot_
cc1: fatal error: arch/arm64/boot/dts/..: No such file or directory
compilation terminated.
```

This looks familiar. Trying to find a patch for this. In `./output/build/host-m4-1.4.18/lib/c-stack.c`: (see https://forums.developer.nvidia.com/t/failed-to-make-l4t-kernel-dts/116399/9)

```
E =
the-space = $E $E
```

### Ubuntu 20.04 on Jetson nano
- https://qengineering.eu/install-ubuntu-20.04-on-jetson-nano.html- https://github.com/Qengineering/Jetson-Nano-Ubuntu-20-image

### Minimal Jetson image
- https://github.com/pythops/jetson-image

### other docker
- https://github.com/2b-t/realsense-ros2-docker

### other robot
- https://www.enthusiasticroboticist.com/blog/ros-2-on-jetson-nano-using-docker/

## Ideas for 3d-reconstruction and mapping
- https://github.com/NVIDIA-ISAAC-ROS/isaac_ros_nvblox
- https://developer.nvidia.com/blog/building-collaborative-robotics-using-ros-and-isaac-sdk/
- https://github.com/NVIDIA-ISAAC-ROS/isaac_ros_visual_slam
- https://developer.nvidia.com/isaac-ros and https://nvidia-isaac-ros.github.io/releases/index.html
- https://github.com/IntelRealSense/realsense-ros/tree/ros2-development
