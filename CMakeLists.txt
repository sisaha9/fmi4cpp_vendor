cmake_minimum_required(VERSION 3.10)
project(fmi4cpp_vendor CXX)

find_package(ament_cmake REQUIRED)
find_package(ament_cmake_vendor_package REQUIRED)

find_package(fmi4cpp 0.8.3 QUIET)

ament_vendor(fmi4cpp_vendor
  SATISFIED ${fmi4cpp_FOUND}
  VCS_URL https://github.com/NTNU-IHB/FMI4cpp.git
  VCS_VERSION v0.8.3
  CMAKE_ARGS
    -DFMI4CPP_BUILD_TESTS:BOOL=OFF
    -DFMI4CPP_BUILD_EXAMPLES:BOOL=OFF
    -DFMI4CPP_USING_CONAN:BOOL=OFF
    -DBUILD_SHARED_LIBS:BOOL=ON
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  list(APPEND AMENT_LINT_AUTO_EXCLUDE
    ament_cmake_cppcheck
  )
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()