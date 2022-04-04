cmake_minimum_required(VERSION 3.15.0...3.21.0)

project(mpicpp VERSION 1.0.0 LANGUAGES CXX)

set(MPICPP_SOURCES mpicpp.cpp)

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

find_package(MPI REQUIRED COMPONENTS CXX)

add_library(mpicpp ${MPICPP_SOURCES})
target_compile_features(mpicpp PUBLIC cxx_std_17)
set_target_properties(mpicpp
  PROPERTIES
  PUBLIC_HEADER mpicpp.hpp)
target_include_directories(mpicpp
  PUBLIC
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>)

target_link_libraries(mpicpp PUBLIC MPI::MPI_CXX)

install(TARGETS mpicpp
  EXPORT mpicpp-targets
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
  PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
  )

configure_package_config_file(
  "${CMAKE_CURRENT_SOURCE_DIR}/config.cmake.in"
  "${CMAKE_CURRENT_BINARY_DIR}/mpicpp-config.cmake"
  INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/mpicpp)
install(FILES
  "${CMAKE_CURRENT_BINARY_DIR}/mpicpp-config.cmake"
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/mpicpp)
install(
  EXPORT mpicpp-targets
  NAMESPACE mpicpp::
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/mpicpp)

