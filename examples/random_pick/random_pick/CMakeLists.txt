cmake_minimum_required(VERSION 2.8.3)
project(cobotta_pick_place)

add_compile_options(-std=c++11)

find_package(Eigen3 REQUIRED)

# Eigen 3.2 (Wily) only provides EIGEN3_INCLUDE_DIR, not EIGEN3_INCLUDE_DIRS
if(NOT EIGEN3_INCLUDE_DIRS)
  set(EIGEN3_INCLUDE_DIRS ${EIGEN3_INCLUDE_DIR})
endif()

find_package(catkin REQUIRED
  COMPONENTS
    moveit_core
    moveit_visual_tools
    moveit_ros_planning
    moveit_ros_planning_interface
    geometric_shapes
    pluginlib
    tf2_ros
    tf2_eigen
    tf2_geometry_msgs
    gpd
)

find_package(Boost REQUIRED system filesystem date_time thread)

catkin_package(
  LIBRARIES
    interactivity_utils
  INCLUDE_DIRS
    ${THIS_PACKAGE_INCLUDE_DIRS}
  CATKIN_DEPENDS
    moveit_core
    moveit_visual_tools
    moveit_ros_planning_interface
    tf2_geometry_msgs
    gpd
  DEPENDS
    EIGEN3
)

###########
## Build ##
###########

include_directories(SYSTEM ${THIS_PACKAGE_INCLUDE_DIRS} ${Boost_INCLUDE_DIR} ${EIGEN3_INCLUDE_DIRS} ${catkin_INCLUDE_DIRS})
# link_directories(${catkin_LIBRARY_DIRS})

option(BUILD_RANDOM_PICK "build random_pick app" OFF)
if(BUILD_RANDOM_PICK STREQUAL "ON")
  add_executable(random_pick src/random_pick.cpp)
  target_link_libraries(random_pick ${catkin_LIBRARIES} ${Boost_LIBRARIES})
  install(TARGETS random_pick DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
endif()

add_executable(fixed_position_pick_place src/fixed_position_pick_place.cpp)
target_link_libraries(fixed_position_pick_place ${catkin_LIBRARIES} ${Boost_LIBRARIES})
install(TARGETS fixed_position_pick_place DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(PROGRAMS scripts/gripper_server.py DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
