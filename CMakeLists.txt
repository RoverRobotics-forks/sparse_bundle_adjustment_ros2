cmake_minimum_required(VERSION 3.5)
project(sparse_bundle_adjustment)

if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

find_package(ament_cmake REQUIRED)
find_package(eigen3_cmake_module REQUIRED)
find_package(Eigen3 REQUIRED)

set(LIBRARY_NAME sba)

add_library(${LIBRARY_NAME}
  src/csparse.cpp
  src/node.cpp
  src/proj.cpp
  src/sba.cpp
  src/sba_file_io.cpp
  src/spa.cpp
  src/spa2d.cpp
  )

target_include_directories(${LIBRARY_NAME}
  PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>)

target_link_libraries(${LIBRARY_NAME}
  # from suitesparse dependency:
  PUBLIC cxsparse
  PUBLIC cholmod
  )

ament_target_dependencies(${LIBRARY_NAME} Eigen3)
target_compile_definitions(${LIBRARY_NAME} PUBLIC SBA_CHOLMOD)
ament_export_interfaces(export_${LIBRARY_NAME} HAS_LIBRARY_TARGET)

install(
  DIRECTORY include/
  DESTINATION include
)
install(
  TARGETS ${LIBRARY_NAME}
  EXPORT export_${LIBRARY_NAME}
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
  INCLUDES DESTINATION include
)

ament_export_dependencies(
  eigen3_cmake_module
  Eigen3
)
ament_export_libraries(${LIBRARY_NAME}
  # we should not need to export these, but we do because https://github.com/ament/ament_cmake/issues/189
  # From suitesparse dependency
  cxsparse
  cholmod
  )
ament_export_include_directories(include)

ament_package()
