cmake_minimum_required(VERSION 3.8)
project(robot_pose_ekf)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()
if(NOT CMAKE_CONFIGURATION_TYPES AND NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()
# bfl (Bayesian Filtering Library) is a third party package that uses pkg-config
find_package(PkgConfig)
pkg_check_modules(BFL REQUIRED orocos-bfl)

include_directories(${BFL_INCLUDE_DIRS})
link_directories(${BFL_LIBRARY_DIRS})


# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(std_msgs REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(tf2_ros REQUIRED)

find_package(Boost REQUIRED COMPONENTS thread)

rosidl_generate_interfaces(${PROJECT_NAME}
  "srv/GetStatus.srv"
  DEPENDENCIES
    builtin_interfaces
    std_msgs
  ADD_LINTER_TESTS
)



include_directories(
    include
    ${Boost_INCLUDE_DIRS}
    )

add_executable(robot_pose_ekf_node
                src/odom_estimation.cpp 
                src/nonlinearanalyticconditionalgaussianodo.cpp 
                src/odom_estimation_node.cpp
)
target_link_libraries(robot_pose_ekf_node
    ${Boost_LIBRARIES}
    ${BFL_LIBRARIES}
    )
ament_target_dependencies(robot_pose_ekf_node rclcpp sensor_msgs nav_msgs geometry_msgs tf2 tf2_geometry_msgs tf2_ros)

install(TARGETS
robot_pose_ekf_node
  DESTINATION lib/${PROJECT_NAME})


ament_export_include_directories(include)
ament_export_dependencies(rosidl_default_runtime)
ament_package()



# install(
#     TARGETS
#         robot_pose_ekf
#     DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

# install(
#     FILES robot_pose_ekf.launch example_with_gps.launch
#     DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

# install(
#     PROGRAMS scripts/wtf.py
#     DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

# ## Tests are failing on OSX for an unknown reason
# include(CMakeDetermineSystem)
# if(CMAKE_SYSTEM_NAME MATCHES "Linux")
# if(CATKIN_ENABLE_TESTING)

# catkin_download_test_data(
#   download_data_ekf_test2_indexed.bag
#   http://download.ros.org/data/robot_pose_ekf/ekf_test2_indexed.bag
#   DESTINATION ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_SHARE_DESTINATION}/test
#   MD5 71addef0ed900e05b301e0b4fdca99e2
# )
# add_executable(test_robot_pose_ekf test/test_robot_pose_ekf.cpp)
# target_link_libraries(test_robot_pose_ekf
#     ${Boost_LIBRARIES}
#     ${catkin_LIBRARIES}
#     ${BFL_LIBRARIES}
#     gtest
#     )
# add_dependencies(test_robot_pose_ekf ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

# catkin_download_test_data(
#   download_data_zero_covariance.bag
#   http://download.ros.org/data/robot_pose_ekf/zero_covariance_indexed.bag
#   DESTINATION ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_SHARE_DESTINATION}/test
#   MD5 1f1f4e361a9e0b0f6b1379b2dd011088
# )
# add_executable(test_robot_pose_ekf_zero_covariance test/test_robot_pose_ekf_zero_covariance.cpp)
# target_link_libraries(test_robot_pose_ekf_zero_covariance
#     ${Boost_LIBRARIES}
#     ${catkin_LIBRARIES}
#     ${BFL_LIBRARIES}
#     gtest
#     )
# add_dependencies(test_robot_pose_ekf_zero_covariance ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

# # This has to be done after we've already built targets, or catkin variables get borked
# find_package(rostest REQUIRED)
# add_rostest(${CMAKE_CURRENT_SOURCE_DIR}/test/test_robot_pose_ekf.launch)
# add_rostest(${CMAKE_CURRENT_SOURCE_DIR}/test/test_robot_pose_ekf_zero_covariance.launch)

# endif(CATKIN_ENABLE_TESTING)
# endif(CMAKE_SYSTEM_NAME MATCHES "Linux")
