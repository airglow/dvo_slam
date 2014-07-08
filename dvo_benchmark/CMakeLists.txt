cmake_minimum_required(VERSION 2.4.6)
#include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)
project(dvo_benchmark)

find_package(catkin REQUIRED COMPONENTS
  cv_bridge
  dvo_core
  dynamic_reconfigure
  eigen_conversions
  tf_conversions
  image_transport
  interactive_markers
  message_filters
  nodelet
  pcl_ros
  roscpp
  sensor_msgs
  tf
  tf_conversions
  cmake_modules
  dvo_ros
  dvo_slam
)


# Set the build type.  Options are:
#  Coverage       : w/ debug symbols, w/o optimization, w/ code-coverage
#  Debug          : w/ debug symbols, w/o optimization
#  Release        : w/o debug symbols, w/ optimization
#  RelWithDebInfo : w/ debug symbols, w/ optimization
#  MinSizeRel     : w/o debug symbols, w/ optimization, stripped binaries
#set(ROS_BUILD_TYPE RelWithDebInfo)


#rosbuild_init()

#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

#uncomment if you have defined messages
#rosbuild_genmsg()
#uncomment if you have defined services
#rosbuild_gensrv()

#common commands for building c++ executables and libraries
#rosbuild_add_library(${PROJECT_NAME} src/example.cpp)
#target_link_libraries(${PROJECT_NAME} another_library)
#rosbuild_add_boost_directories()
#rosbuild_link_boost(${PROJECT_NAME} thread)
#rosbuild_add_executable(example examples/example.cpp)
#target_link_libraries(example ${PROJECT_NAME})

include_directories(include)
# Eigen3 library
find_package(Eigen REQUIRED)
include_directories(${Eigen_INCLUDE_DIRS})

# VTK library
find_package(VTK REQUIRED)
include_directories(${VTK_INCLUDE_DIRS})
link_directories(${VTK_LIBRARY_DIRS})


find_package(G2O)

include_directories(
  ${catkin_INCLUDE_DIRS}
)

add_executable(benchmark
     src/benchmark_slam.cpp
)

#rosbuild_add_executable(experiment
#     src/experiment.cpp
#)

target_link_libraries(benchmark
  vtkCommon
  ${catkin_LIBRARIES}
  ${G2O_LIBRARIES}
)

# create output directory - just convenience for our users
file(MAKE_DIRECTORY "${PROJECT_SOURCE_DIR}/output")
file(MAKE_DIRECTORY "${PROJECT_SOURCE_DIR}/output/video")
