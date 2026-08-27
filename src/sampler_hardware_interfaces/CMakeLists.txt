cmake_minimum_required(VERSION 3.16)
project(sampler_hardware_interfaces)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Dependencies
find_package(ament_cmake REQUIRED)
find_package(hardware_interface REQUIRED)
find_package(pluginlib REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclcpp_lifecycle REQUIRED)
find_package(rex_interfaces REQUIRED)
find_package(libVescCan REQUIRED)

# Hardware interface library
add_library(${PROJECT_NAME} SHARED
  src/sampler_hardware.cpp
  src/RosCanConstants.cpp
)

target_include_directories(${PROJECT_NAME}
  PUBLIC
    include
)

ament_target_dependencies(${PROJECT_NAME}
  hardware_interface
  pluginlib
  rclcpp
  rclcpp_lifecycle
  rex_interfaces
)

target_link_libraries(${PROJECT_NAME}
  libVescCan::libVescCan
)

# Export dependencies
ament_export_dependencies(
  rex_interfaces
)

ament_export_libraries(${PROJECT_NAME})

# Export ros2_control hardware plugin
pluginlib_export_plugin_description_file(
  hardware_interface
  sampler_hardware_interfaces.xml
)

# Install library
install(
  TARGETS ${PROJECT_NAME}
  LIBRARY DESTINATION lib
)

install(
  DIRECTORY include/
  DESTINATION include
)

install(
  FILES sampler_hardware_interfaces.xml
  DESTINATION share/${PROJECT_NAME}
)

ament_package()