cmake_minimum_required(VERSION 3.9)

project(fmi4cpp_vendor)

find_package(ros_environment REQUIRED)

if($ENV{ROS_VERSION} EQUAL 1)
  find_package(catkin REQUIRED)
elseif($ENV{ROS_VERSION} EQUAL 2)
  find_package(ament_cmake REQUIRED)
endif()

macro(build_fmi4cpp)
  set(git_tag "v0.8.3")
  set(extra_cmake_args)

  get_property(multi_config GLOBAL PROPERTY GENERATOR_IS_MULTI_CONFIG)
  if(NOT multi_config AND DEFINED CMAKE_BUILD_TYPE)
    list(APPEND extra_cmake_args -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE})
  endif()

  include(ExternalProject)
  externalproject_add(fmi4cpp-${git_tag}
    GIT_REPOSITORY https://github.com/NTNU-IHB/FMI4cpp.git
    GIT_TAG ${git_tag}
    GIT_SHALLOW ON
    TIMEOUT 60
    # Suppress git update due to https://gitlab.kitware.com/cmake/cmake/-/issues/16419
    UPDATE_COMMAND ""
    CMAKE_ARGS
      -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/fmi4cpp_install
      ${extra_cmake_args}
  )

  # The external project will install to the build folder, but we'll install that on make install.
  install(
    DIRECTORY
      ${CMAKE_CURRENT_BINARY_DIR}/fmi4cpp_install/
    DESTINATION
      ${CMAKE_INSTALL_PREFIX}
    USE_SOURCE_PERMISSIONS
    PATTERN config.h EXCLUDE
  )
endmacro()

build_fmi4cpp()

if($ENV{ROS_VERSION} EQUAL 1)
  catkin_package(
    LIBRARIES fmi4cpp
    DEPENDS fmi4cpp
    CFG_EXTRAS fmi4cpp_vendor-extras.cmake)
elseif($ENV{ROS_VERSION} EQUAL 2)
  ament_export_libraries(fmi4cpp)
  ament_export_dependencies(fmi4cpp)

  if(BUILD_TESTING)
    find_package(ament_lint_auto REQUIRED)
    ament_lint_auto_find_test_dependencies()
  endif()

  ament_package(CONFIG_EXTRAS cmake/fmi4cpp_vendor-extras.cmake)
endif()