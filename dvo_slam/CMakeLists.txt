cmake_minimum_required(VERSION 2.8.3)
project(dvo_slam)

add_definitions(-msse)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  cmake_modules cv_bridge
  tf_conversions
  dvo_core
  dvo_ros
  dynamic_reconfigure
  image_transport
  interactive_markers
  #libg2o
  message_filters
  roscpp
  sensor_msgs
  visualization_msgs
  sophus
)

## System dependencies are found with CMake's conventions
 find_package(Boost REQUIRED COMPONENTS system thread)
 #find_package(Boost REQUIRED COMPONENTS system thread bind signals2)

#find_package(G2O REQUIRED)
#find_package(G2O)

set(G2O_LIBRARIES
	g2o_cli
	g2o_core
	g2o_csparse_extension
	g2o_ext_csparse
	g2o_ext_freeglut_minimal
	g2o_incremental
	g2o_interactive
	g2o_interface
	g2o_opengl_helper
	g2o_parser
	g2o_simulator
	g2o_solver_cholmod
	g2o_solver_csparse
	g2o_solver_dense
	g2o_solver_eigen
	g2o_solver_pcg
	g2o_solver_slam2d_linear
	g2o_solver_structure_only
	g2o_stuff
	g2o_types_data
	g2o_types_icp
	g2o_types_sba
	g2o_types_sclam2d
	g2o_types_sim3
	g2o_types_slam2d
	g2o_types_slam2d_addons
	g2o_types_slam3d
	g2o_types_slam3d_addons
	)

## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

################################################
## Declare ROS messages, services and actions ##
################################################

## To declare and build messages, services or actions from within this
## package, follow these steps:
## * Let MSG_DEP_SET be the set of packages whose message types you use in
##   your messages/services/actions (e.g. std_msgs, actionlib_msgs, ...).
## * In the file package.xml:
##   * add a build_depend and a run_depend tag for each package in MSG_DEP_SET
##   * If MSG_DEP_SET isn't empty the following dependencies might have been
##     pulled in transitively but can be declared for certainty nonetheless:
##     * add a build_depend tag for "message_generation"
##     * add a run_depend tag for "message_runtime"
## * In this file (CMakeLists.txt):
##   * add "message_generation" and every package in MSG_DEP_SET to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * add "message_runtime" and every package in MSG_DEP_SET to
##     catkin_package(CATKIN_DEPENDS ...)
##   * uncomment the add_*_files sections below as needed
##     and list every .msg/.srv/.action file to be processed
##   * uncomment the generate_messages entry below
##   * add every package in MSG_DEP_SET to generate_messages(DEPENDENCIES ...)

generate_dynamic_reconfigure_options(
  cfg/dvo_slam.cfg
  cfg/graph_visualizer.cfg
)


## Generate messages in the 'msg' folder
 add_message_files(
   FILES
   PoseStampedArray.msg
 )

## Generate services in the 'srv' folder
# add_service_files(
#   FILES
#   Service1.srv
#   Service2.srv
# )

## Generate actions in the 'action' folder
# add_action_files(
#   FILES
#   Action1.action
#   Action2.action
# )

## Generate added messages and services with any dependencies listed here
 generate_messages(
   DEPENDENCIES
   sensor_msgs   visualization_msgs
 )

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if you package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
  INCLUDE_DIRS include
  LIBRARIES dvo_slam
  CATKIN_DEPENDS cmake_modules cv_bridge dvo_core dvo_ros dynamic_reconfigure image_transport interactive_markers message_filters roscpp sensor_msgs visualization_msgs sophus
#  DEPENDS system_lib
)

###########
## Build ##
###########

include_directories(include)
include_directories(/usr/include/suitesparse)
# VTK library
find_package(VTK REQUIRED)
include_directories(${VTK_INCLUDE_DIRS})

#find_package(tbb REQUIRED)
#include_directories(${VTK_INCLUDE_DIRS})

#find_package(Eigen REQUIRED)
#include_directories(${VTK_INCLUDE_DIRS})

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
  ${catkin_INCLUDE_DIRS}
)

include_directories(
  ${G2O_INCLUDE_DIRS}
)

## Declare a cpp library
add_library(dvo_slam
  src/keyframe_tracker.cpp
  src/keyframe_graph.cpp
  src/keyframe_constraint_search.cpp
  src/camera_keyframe_tracking.cpp
  
  src/config.cpp
  
  src/tracking_result_evaluation.cpp
  src/local_map.cpp
  src/local_tracker.cpp
  
  src/constraints/constraint_proposal.cpp
  src/constraints/constraint_proposal_voter.cpp
  src/constraints/constraint_proposal_validator.cpp
  
  src/serialization/map_serializer_interface.cpp
  src/serialization/map_serializer.cpp
  
  src/visualization/graph_visualizer.cpp
  
  src/types.cpp
)

## Declare a cpp executable
add_executable(camera_keyframe_tracker   src/camera_keyframe_tracker_node.cpp)

## Add cmake target dependencies of the executable/library
## as an example, message headers may need to be generated before nodes
# add_dependencies(dvo_slam_node dvo_slam_generate_messages_cpp)

## Specify libraries to link a library or executable target against
 target_link_libraries(dvo_slam
   ${catkin_LIBRARIES}
   ${G2O_LIBRARIES}
   dvo_ros
   cxsparse 

 )

 target_link_libraries(camera_keyframe_tracker
   ${catkin_LIBRARIES}
   ${G2O_LIBRARIES}
   dvo_ros
   dvo_slam
   cxsparse
 )

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
# install(TARGETS dvo_slam dvo_slam_node
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_dvo_slam.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
