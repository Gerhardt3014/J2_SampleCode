# Matrix sample code

- [Matrix sample code](#matrix-sample-code)
  - [Introduction](#introduction)
  - [Build](#build)
    - [windows](#windows)
    - [linux](#linux)
    - [ROS](#ros)
      - [1. Generate ROS package](#1-generate-ros-package)
      - [2. Integrate to ROS workspace](#2-integrate-to-ros-workspace)
    - [arm_hisi_v500](#arm_hisi_v500)
  - [Document](#document)

## Introduction

matrix-sample-code is used to help the customers who take Horizon Journey 2 Mono and Quad (hereinafter called "J2 Mono" and "J2 Quad") as their visual perception solution, helping them parse the structured perception data, which is received via Ethernet. 
matrix-sample-code is based on Google Protocol Buffer (Protobuf) and Zero Message Queue (ZMQ) libraries.

Supported platforms:

- windows 10
- Linux
- ROS

## Build

### windows

Requirements:

- windows 10
- visual studio 2015 / 2017 / 2019
- cmake (>=3.0)

On windows platform, it uses MSVE to complie and support x86 and amd64 architectures (amd64 is the default architecture). It will generate two tarets (parse-protocol and unpack) by default, the parse-protocol is used to parse perception results and sensors data, the unpack is used to extract image from pack file. You can chose the specific target to generate on cmake command line by close *BUILD_PARSE_PROTOCOL* or *BUILD_UNPACK*.

Building steps:

1. Entry project root directory and new a build directory:  

   ```sh
   cd matrix-sample-code
   mkdir build
   ```

2. Entry the *build* directory:

    ```sh
    cd build
    ```

3. Configure and generate project by cmake：

    ```sh
    # visual studio 2015 win32
    cmake -G "Visual Studio 14 2015" ..

    # visual studio 2015 win64
    cmake -G "Visual Studio 14 2015 Win64" ..

    # visual studio 2017 win32
    cmake -G "Visual Studio 15 2017" ..

    # visual studio 2017 win64
    cmake -G "Visual Studio 15 2017 Win64" ..

    # visual studio 2019 win32
    cmake -G "Visual Studio 16 2019" -A Win32 ..

    # visual studio 2019 win64
    cmake -G "Visual Studio 16 2019" -A x64 ..
    ```sh

    It will generate two targets in the project(parse-protocol 和 unpack). You can use the *BUILD_PARSE_PROTOCOL* and *BUILD_UNPACK* configure the target or not. For example, it will not generate *unpack* target using following command: `cmake .. -G "Visual Studio 14 2015" -DBUILD_UNPACK=OFF`.

4. If it hasn't compile proto files which locat on `protocol/raw/*.proto`, need compile them by using following command: `cmake --build . --target build-protocol`. Otherwise, just skip this step.  

   **Note:** The step `3` always generate a taret `build-protocol`, which do not depend platform and architecture, to compile proto files to `*.pb.cc` and `*.pb.h` files.

5. Building targets:

    ```sh
    # building Debug verson on shell
    cmake --build . --config Debug

    # building Release verson on shell
    cmake --build . --config Release
    ```

    Besides, You can use visual studio open the *matrix-sample-code.sln* to compile the specific target.

### linux

Requirements:

- ubuntu (14.04 / 16.04 / 18.04)
- gcc (gcc-4.8 / gcc-5.4 / gcc-7.5)
- cmake (>=3.0)

The supported toolchains on linux platfrom are gcc-4.8 and gcc-5.4. It will generate two targets (parse-protocol and unpack) by default.

Building steps:

1. Entry project root directory and new a build directory:  

   ```{sh}
   cd matrix-sample-code
   mkdir build
   ```

2. Entry the *build* directory:

    ```sh
    cd build
    ```

3. Configure and generate project by cmake：

    ```sh
    # the default compiler on system: gcc-4.8
    cmake ..

    # config compiler: supposing gcc-4.8 installed on /usr/bin/
    cmake -DCMAKE_C_COMPILER=/usr/bin/gcc-4 -DCMAKE_CXX_COMPILER=/usr/bin/g++-4 -DTOOL_CHAIN=gcc-4.8 ..

    # the default compiler on system: gcc-5.4
    cmake -DTOOL_CHAIN=gcc-5.4 ..

    # config compiler: supposing gcc-5.4 installed on /usr/bin/usr/bin/
    cmake -DCMAKE_C_COMPILER=/usr/bin/gcc-5 -DCMAKE_CXX_COMPILER=/usr/bin/g++-5 -DTOOL_CHAIN=gcc-5.4 ..

    # the default compiler on system: gcc-7.5
    cmake -DTOOL_CHAIN=gcc-7.5 ..

    # config compiler: supposing gcc-7.5 installed on /usr/bin/usr/bin/
    cmake -DCMAKE_C_COMPILER=/usr/bin/gcc-7 -DCMAKE_CXX_COMPILER=/usr/bin/g++-7 -DTOOL_CHAIN=gcc-7.5 ..
    ```

    The project set *gcc-4.8* as the default tool chain, so when you use other tool chain, you should config it by TOOL_CHAIN on command line.

4. Building targets:

    ```sh
    cmake --build .

    # or
    make

### ROS

Requirements:

- ubuntu 16.04 or ubuntu 18.04
- ROS Kinetic or melodic
- gcc-5.4 or gcc-7.5
- python 3.5 (or later)

#### 1. Generate ROS package

It's provided a python script to generate ROS package (This script should work on python 3 environment, it not the same python environment of ROS system. If your ROS environment is using python 2, you'd better use a virtual python env to generate ROS package).

You can use following command to generate ROS package:

```sh
python generate_ros_package.py -r kinetic -i /home/a/matrix-same-code -o /home/a/out
```

Here is the explanation of command parameters above:

- `-r` indicate ros version, 'kinetic' means packages generating for ROS Kinetic.
- `-i` indicate the absolute path of *matrix-sample-code* package.
- `-o` indicate the absolute path of output directory on which the ROS packages generated will locate.

Than, there is a 'matrix-sample-code-ros-package' directory on /home/a/out, it contains all the ros packages (there is only one package 'matrix').

#### 2. Integrate to ROS workspace

There is no workspace (workspace is a concept of ROS, details refer to [ROS WIKI](http://wiki.ros.org/)).

```sh
# init ros workspace
cd /home/a
mkdir -p ros_workspace/src
cd /home/a/ros_workspace
catkin_make
source devel/setup.bash

# create matrix package (empty)
cd /home/a/ros_workspace/src
catkin_create_pdk matrix

# cover empty matrix package
cp -r /home/a/out/matrix-sample-code-ros-package/matrix /home/a/ros_workspace/src/matrix

# build the whole workspace
catkin_make
```

Integrating matrix package to existed workspace:

```sh
# suppose workspace is /home/a/ros_workspace
# create matrix package (empty)
cd /home/a/ros_workspace/src
catkin_create_pdk matrix

# cover empty matrix package
cp -r /home/a/out/matrix-sample-code-ros-package/matrix /home/a/ros_workspace/src/matrix

# build the whole workspace
catkin_make
```

Details refer to [How to generate and integrate into workspace](doc/developer_guide/sample_code/sample_code.md#ros).

### arm_hisi_v500

Requirements:

- ubuntu
- toolchain(arm_hisi_v500)
- cmake(>=3.0)

Using *arm_hisi_v500* toolchain cross compile *parse-protocol-nabula-v3* target on linux. BUILD_PARSE_PROTOCOL_NABULA_V3 is closed by default, So you should open it when you configure and generate project.

Building steps:

1. Entry project root directory and new a build directory:

   ```sh
   cd matrix-sample-code
   mkdir build
   ```

2. Entry the *build* directory:

    ```sh
    cd build
    ```

3. Configure and generate project by cmake:
  
    ```sh
    cmake -DBUILD_PARSE_PROTOCOL_NABULA_V3=ON -DCMAKE_C_COMPILER=arm-hisiv500-linux-gcc -DCMAKE_CXX_COMPILER=arm-hisiv500-linux-g++ -DTOOL_CHAIN=arm_hisi_v500 ..
    ```

4. Building target:

    ```sh
    cmake --build .

    # or
    make
    ```

## Document

- [Matrix Developer Guide](doc/developer_guide/README.md)
- [About Source Code](doc/html/index.html)

## H265 Decoding
    For now, H265 decoding is only supported in linux gcc7.5, `H265_DECODER_UNPACK` is set default to `ON` in unpack. The decoding for H265 need two libs: 
    `libswresample.so.2` and `libx265.so.146` and is stored in `3rd/linux/gcc-7.5/ffmpeg/lib`, please export this path to your `LD_LIBRARY_PATH`.#   J 2 _ S a m p l e C o d e  
 # J2_SampleCode
