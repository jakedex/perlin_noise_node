cmake_minimum_required(VERSION 2.8.3)
project(perlin_noise_node)

##############################################################################
# Find dependencies
##############################################################################

## Declare main C++ dependencies
#set(ROS_CXX_DEPENDENCIES
#  naoqi_bridge_msgs
#  roscpp
#  std_msgs
#  std_srvs
#  geometry_msgs
#  angles
#)

set(THIS_PACKAGE_ROS_DEPS
 roscpp
 std_msgs
 naoqi_bridge_msgs
 filters
 perlin_noise_filter
)

find_package(catkin REQUIRED COMPONENTS ${THIS_PACKAGE_ROS_DEPS})

##############################################################################
# Define package
##############################################################################

catkin_package(
  CATKIN_DEPENDS roscpp std_msgs naoqi_bridge_msgs filters perlin_noise_filter
)

##############################################################################
# Build
##############################################################################

include_directories(include ${catkin_INCLUDE_DIRS})

add_executable(nao_pn_gen src/perlin_base.cpp src/nao.cpp src/nao_node.cpp)
target_link_libraries(nao_pn_gen ${catkin_LIBRARIES})

##############################################################################
# Install
##############################################################################

# Install headers
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)

install(TARGETS nao_pn_gen
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

install(DIRECTORY launch
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
