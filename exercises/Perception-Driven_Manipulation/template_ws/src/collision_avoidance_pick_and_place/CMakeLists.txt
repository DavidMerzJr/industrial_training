cmake_minimum_required(VERSION 2.8.3)
project(collision_avoidance_pick_and_place)
add_definitions (-std=c++14)
find_package(catkin REQUIRED COMPONENTS 
  actionlib
  industrial_robot_client
  industrial_robot_simulator
  message_generation
  moveit_msgs
  moveit_ros_manipulation
  moveit_ros_move_group
  moveit_ros_planning_interface
  object_manipulation_msgs
  pcl_msgs
  pcl_ros
  shape_msgs
  std_msgs
  robot_io
  roscpp
  tf_conversions
)

find_package(PCL REQUIRED)

include_directories(include
  ${catkin_INCLUDE_DIRS}
  ${PCL_INCLUDE_DIRS}
)

#######################################
## Declare ROS messages and services ##
#######################################
## Generate services in the 'srv' folder
add_service_files(
   FILES
   GetTargetPose.srv
)

## Generate added messages and services with any dependencies listed here
generate_messages(
  DEPENDENCIES   
    pcl_msgs
    shape_msgs
    std_msgs)

###################################
## catkin specific configuration ##
###################################
catkin_package(
  CATKIN_DEPENDS
    actionlib
    industrial_robot_client
    industrial_robot_simulator
    message_runtime
    moveit_msgs
    moveit_ros_manipulation
    moveit_ros_move_group
    moveit_ros_planning_interface
    object_manipulation_msgs
    pcl_msgs
    pcl_ros
    robot_io
    roscpp
    shape_msgs
    std_msgs
    tf_conversions
  DEPENDS
    PCL
)

###########
## Build ##
###########

add_executable(target_recognition_service src/services/target_recognition_service.cpp)
add_dependencies(target_recognition_service ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(target_recognition_service ${catkin_LIBRARIES} ${PCL_LIBRARIES})

add_executable(generate_cloud_node src/nodes/generate_point_cloud.cpp)
add_dependencies(generate_cloud_node ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(generate_cloud_node ${catkin_LIBRARIES} ${PCL_LIBRARIES})

add_executable(pick_and_place_node 
  src/nodes/pick_and_place_node.cpp
  src/tasks/move_to_wait_position.cpp
  src/tasks/set_gripper.cpp
  src/tasks/reset_world.cpp
  src/tasks/set_attached_object.cpp
  src/tasks/detect_box_pick.cpp
  src/tasks/create_pick_moves.cpp
  src/tasks/create_place_moves.cpp
  src/tasks/pickup_box.cpp
  src/tasks/place_box.cpp	
  src/tasks/create_motion_plan.cpp
  src/utilities/pick_and_place_utilities.cpp)
add_dependencies(pick_and_place_node ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(pick_and_place_node ${catkin_LIBRARIES} ${PCL_LIBRARIES})

