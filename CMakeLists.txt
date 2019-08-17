cmake_minimum_required(VERSION 3.5)
project(sparse_bundle_adjustment)

if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

find_package(ament_cmake REQUIRED)

add_library(sba
  src/sba.cpp src/spa.cpp src/spa2d.cpp src/csparse.cpp src/proj.cpp src/node.cpp src/sba_file_io.cpp)

target_include_directories(sba
  PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>)

find_package(eigen3_cmake_module REQUIRED)
find_package(Eigen3 REQUIRED)

ament_target_dependencies(sba Eigen3)

target_compile_definitions(sba PRIVATE SBA_CHOLMOD)

ament_export_interfaces(export_sba HAS_LIBRARY_TARGET)
ament_export_dependencies(
  eigen3_cmake_module
  Eigen3
)
target_link_libraries(sba blas lapack cholmod cxsparse)

install(DIRECTORY include/
        DESTINATION include/)

install(
  TARGETS sba
  EXPORT export_sba
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  INCLUDES DESTINATION include
)
ament_package()
