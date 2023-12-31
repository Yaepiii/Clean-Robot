cmake_minimum_required(VERSION 2.8.3)
project(mw-exploration)

## Find catkin macros and libraries
find_package(catkin REQUIRED COMPONENTS
  actionlib
  costmap_2d
  geometry_msgs
  map_msgs
  move_base_msgs
  nav_msgs
  roscpp
  std_msgs
  tf
  visualization_msgs
  exploration_msgs
  dynamic_reconfigure
)

generate_dynamic_reconfigure_options(
  config/Explore.cfg
)

###################################
## catkin specific configuration ##
###################################
catkin_package(
  CATKIN_DEPENDS
    actionlib
    costmap_2d
    geometry_msgs
    map_msgs
    move_base_msgs
    nav_msgs
    roscpp
    std_msgs
    tf
    visualization_msgs
    exploration_msgs
    dynamic_reconfigure
)

###########
## Build ##
###########
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

## Specify additional locations of header files
include_directories(
  ${catkin_INCLUDE_DIRS}
  include
)

add_executable(explore_server
  src/costmap_client.cpp
  src/explore_server.cpp
  src/frontier_search.cpp
)

add_dependencies(explore_server ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(explore_server ${catkin_LIBRARIES})
add_dependencies(explore_server ${PROJECT_NAME}_gencfg)

add_executable(plugin_client
  src/plugin_client.cpp
)

add_dependencies(plugin_client ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(plugin_client ${catkin_LIBRARIES})

#############
## Install ##
#############

# install nodes
install(TARGETS explore_server
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

# install include
install(DIRECTORY include/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

# install roslaunch files
install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
)

#############
## Testing ##
#############
if(CATKIN_ENABLE_TESTING)
  find_package(roslaunch REQUIRED)

  # test all launch files
  roslaunch_add_file_check(launch)
endif()
