
cmake_minimum_required(VERSION 3.5)
project(ros_deep_learning)

# locate dependencies
find_package(jetson-utils REQUIRED)
find_package(jetson-inference REQUIRED)

find_package(CUDA REQUIRED)

# detect ROS version
string(TOUPPER $ENV{ROS_DISTRO} ROS_DISTRO)
set(ROS_DISTRO "ROS_${ROS_DISTRO}")
message("detected ROS_DISTRO=${ROS_DISTRO}")

find_package(VPI 2.0)
link_directories(/usr/lib/aarch64-linux-gnu/tegra)

find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(vision_msgs REQUIRED)

set(CMAKE_CXX_STANDARD 14)
include_directories(${CUDA_INCLUDE_DIRS})
add_definitions(-DROS2 -DROS_DISTRO=${ROS_DISTRO})

# sources shared across nodes
set(common_src src/image_converter.cpp src/ros_compat.cpp)

# inference nodes
add_executable(video_source src/node_video_source.cpp ${common_src})
target_link_libraries(video_source ${catkin_LIBRARIES} jetson-inference)

ament_target_dependencies(video_source rclcpp std_msgs sensor_msgs vision_msgs)
install(TARGETS video_source DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY launch DESTINATION share/${PROJECT_NAME}/)

ament_package()

