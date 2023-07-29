cmake_minimum_required(VERSION 3.0.2)
project(orb_slam3_ros_wrapper)

# Change this to your installation of ORB-SLAM3. Default is ~/
set(ORB_SLAM3_DIR
   $ENV{HOME}/Documents/dev/ORB_SLAM3
)
file(COPY ${ORB_SLAM3_DIR}/Vocabulary/ORBvoc.txt
     DESTINATION ${PROJECT_SOURCE_DIR}/config)
file(COPY ${ORB_SLAM3_DIR}/Examples/Monocular/KITTI00-02.yaml
     DESTINATION ${PROJECT_SOURCE_DIR}/config)

set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_FLAGS "-std=c++14")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall -g")

find_package (catkin REQUIRED COMPONENTS
   roscpp
   rospy
   std_msgs
   cv_bridge
   image_transport
   tf
   sensor_msgs
   message_generation
)

# find_package(OpenCV REQUIRED)
find_package(OpenCV 3.4.20 EXACT PATHS /home/ubuntu/Documents/dev/cpp_lib/opencv3/install/share/OpenCV)
# find_package(OpenCV 4.4 EXACT PATHS ${ORB_SLAM3_DIR}/Thirdparty/OpenCV/install/lib/cmake/opencv4)
MESSAGE("OPENCV VERSION:")
MESSAGE(${OpenCV_VERSION})

# find_package(Eigen3 REQUIRED)
find_package(Eigen3 3.3.4 EXACT REQUIRED PATHS ${ORB_SLAM3_DIR}/Thirdparty/Eigen3/install/share/eigen3/cmake)
MESSAGE("Eigen3 VERSION:")
MESSAGE(${Eigen3_VERSION})

# find_package(Pangolin REQUIRED)
find_package(Pangolin REQUIRED HINTS ${ORB_SLAM3_DIR}/Thirdparty/Pangolin/install/lib/cmake/Pangolin)

find_package(easy_profiler 2.1.0 EXACT REQUIRED PATHS ${ORB_SLAM3_DIR}/Thirdparty/EasyProfiler/install/lib/cmake/easy_profiler)

catkin_package (
   # CATKIN_DEPENDS roscpp rospy std_msgs image_transport tf sensor_msgs message_runtime
   CATKIN_DEPENDS roscpp rospy std_msgs cv_bridge image_transport tf sensor_msgs message_runtime
   LIBRARIES {PROJECT_NAME}
)

include_directories(
   ${ORB_SLAM3_DIR}
   ${ORB_SLAM3_DIR}/include
   ${ORB_SLAM3_DIR}/include/CameraModels
   ${ORB_SLAM3_DIR}/Thirdparty/Sophus
   ${PROJECT_SOURCE_DIR}/include
   ${EIGEN3_INCLUDE_DIR}
   ${catkin_INCLUDE_DIRS}
   ${Pangolin_INCLUDE_DIRS}
)

# Build ROS nodes
set(LIBS
   ${ORB_SLAM3_DIR}/lib/libORB_SLAM3.so
   ${EIGEN3_LIBS}
   ${catkin_LIBRARIES}
   ${Pangolin_LIBRARIES}
   ${OpenCV_LIBS}
   easy_profiler
)

# mono node
add_executable (${PROJECT_NAME}_mono
   src/mono_node.cc
   src/common.cc
)
target_link_libraries(${PROJECT_NAME}_mono
   ${LIBS}
)


# stereo node
add_executable (${PROJECT_NAME}_stereo
   src/stereo_node.cc
   src/common.cc
)
target_link_libraries(${PROJECT_NAME}_stereo
   ${LIBS}
)


# mono-inertial node
add_executable (${PROJECT_NAME}_mono_inertial
   src/mono_inertial_node.cc
   src/common.cc
)
target_link_libraries(${PROJECT_NAME}_mono_inertial
   ${LIBS}
)


# stereo-inertial node
add_executable (${PROJECT_NAME}_stereo_inertial
   src/stereo_inertial_node.cc
   src/common.cc
)
target_link_libraries(${PROJECT_NAME}_stereo_inertial
   ${LIBS}
)


# RGB-D node
add_executable (${PROJECT_NAME}_rgbd
   src/rgbd_node.cc
   src/common.cc
)
target_link_libraries(${PROJECT_NAME}_rgbd
   ${LIBS}
)