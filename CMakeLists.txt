cmake_minimum_required(VERSION 3.5)
project(go1_republisher)

# add_compile_options(-std=c++11)

## Find catkin and any catkin packages
find_package(ament_cmake REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(rclpy REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(unitree_legged_sdk REQUIRED)
# find_package(camera_info_manager_py REQUIRED)
# find_package(cv_bridge REQUIRED)
# find_package(image_transport REQUIRED)
# find_package(OpenCV 4 REQUIRED)

# unitree_legged_sdk's ament export records its source-tree .a path, which is
# deleted after the deps workspace build. Derive the install prefix from the
# cmake config dir and locate the installed static library directly.
get_filename_component(_unitree_prefix "${unitree_legged_sdk_DIR}/../../.." ABSOLUTE)
find_library(UNITREE_SDK_LIB
  NAMES unitree_legged_sdk
  HINTS "${_unitree_prefix}/lib/unitree_legged_sdk"
  NO_DEFAULT_PATH
)
if(NOT UNITREE_SDK_LIB)
  message(FATAL_ERROR "unitree_legged_sdk .a not found under ${_unitree_prefix}/lib/unitree_legged_sdk")
endif()

set(EXTRA_LIBS -pthread)

include_directories(
	include
	# ${catkin_INCLUDE_DIRS}
	# ${OpenCV_INCLUDE_DIRS}
	${unitree_legged_sdk_INCLUDE_DIRS}
	# ${CMAKE_SOURCE_DIR}/../UnitreecameraSDK/include
)

add_executable(imu_odom src/imu_odom.cpp)
target_link_libraries(imu_odom ${EXTRA_LIBS} ${UNITREE_SDK_LIB})
ament_target_dependencies(imu_odom tf2_ros rclpy rclcpp std_msgs nav_msgs sensor_msgs)

# add_executable(camera src/camera.cpp)
# target_link_libraries(camera ${EXTRA_LIBS} ${OpenCV_LIBRARIES})
# add_dependencies(camera ${${PROJECT_NAME}_EXPORTED_TARGETS})

install(TARGETS imu_odom
  DESTINATION lib/${PROJECT_NAME}
)

ament_package()