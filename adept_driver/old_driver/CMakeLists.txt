cmake_minimum_required(VERSION 2.8.3)
project(adept_driver)
# Load catkin and all dependencies required for this package
find_package(catkin REQUIRED COMPONENTS std_msgs roscpp actionlib
    trajectory_msgs actionlib_msgs control_msgs sensor_msgs urdf
    industrial_msgs industrial_robot_client industrial_utils simple_message roslint)
find_package(Boost REQUIRED COMPONENTS system thread)

catkin_package(
  CATKIN_DEPENDS industrial_robot_client
)

add_definitions(-DLINUXSOCKETS=1)
add_definitions(-DROS=1)

include_directories(
  include
  ${Boost_INCLUDE_DIRS}
  ${catkin_INCLUDE_DIRS}
)

add_executable(${PROJECT_NAME}_robot_state
  src/robot_state.cpp
  src/joint_relay_handler.cpp)
target_link_libraries(${PROJECT_NAME}_robot_state
  industrial_robot_client
  simple_message
  ${catkin_LIBRARIES})

add_executable(${PROJECT_NAME}_motion_streaming_interface
  src/motion_streaming_interface.cpp
  src/joint_trajectory_handler.cpp
  src/joint_trajectory_downloader.cpp)
target_link_libraries(${PROJECT_NAME}_motion_streaming_interface
  ${Boost_libraries})

add_executable(${PROJECT_NAME}_joint_trajectory_action
  src/joint_trajectory_action.cpp)
target_link_libraries(${PROJECT_NAME}_joint_trajectory_action
  ${Boost_libraries})

install(TARGETS
    ${PROJECT_NAME}_robot_state
    ${PROJECT_NAME}_motion_streaming_interface
    ${PROJECT_NAME}_joint_trajectory_action
  DESTINATION
    ${CATKIN_PACKAGE_BIN_DESTINATION}
)

foreach(dir launch V+)
  install(
    DIRECTORY ${dir}/
    DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/${dir})
endforeach()
