cmake_minimum_required(VERSION 3.20)

project(Detours CXX)
set(Detours_VERSION 4.0.1)

set(CMAKE_EXPORT_COMPILE_COMMANDS 1)


set(DETOURS_ROOT_DIR ${CMAKE_CURRENT_SOURCE_DIR}/Detours)
set(DETOURS_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/Detours/src)

set(DETOURS_HEADERS
  ${DETOURS_SOURCE_DIR}/detours.h
  ${DETOURS_SOURCE_DIR}/detver.h)

set(DETOURS_SOURCE_FILES
  ${DETOURS_SOURCE_DIR}/detours.cpp
  ${DETOURS_SOURCE_DIR}/modules.cpp
  ${DETOURS_SOURCE_DIR}/disasm.cpp
  ${DETOURS_SOURCE_DIR}/image.cpp
  ${DETOURS_SOURCE_DIR}/creatwth.cpp
  ${DETOURS_SOURCE_DIR}/disolx86.cpp
  ${DETOURS_SOURCE_DIR}/disolx64.cpp
  ${DETOURS_SOURCE_DIR}/disolia64.cpp
  ${DETOURS_SOURCE_DIR}/disolarm.cpp
  ${DETOURS_SOURCE_DIR}/disolarm64.cpp
  ${DETOURS_SOURCE_DIR}/uimports.cpp
  )

set_property(
  SOURCE ${DETOURS_SOURCE_DIR}/uimports.cpp
  APPEND PROPERTY HEADER_FILE_ONLY true)

add_library(detours STATIC ${DETOURS_HEADERS} ${DETOURS_SOURCE_FILES})
add_library(Detours::detours ALIAS detours)

target_compile_definitions(detours PRIVATE
  WIN32_LEAN_AND_MEAN
  _WIN32_WINNT=0x501
  )

set(DETOURS_INCLUDE_DIR ${CMAKE_CURRENT_BINARY_DIR}/include/)

foreach(f IN LISTS DETOURS_HEADERS)
  get_filename_component(header ${f} NAME)
  configure_file(${f} ${DETOURS_INCLUDE_DIR}/detours/${header} COPYONLY)
endforeach()

# set_target_properties(detours PROPERTIES
#   INTERFACE_INCLUDE_DIRECTORIES ${DETOURS_INCLUDE_DIR})

# target_include_directories(fmt PUBLIC
#   $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

export(TARGETS detours NAMESPACE Detours:: FILE ${CMAKE_CURRENT_BINARY_DIR}/DetoursTargets.cmake)

# include(CMakePackageConfigHelpers)
# write_basic_package_version_file(
#   "${CMAKE_CURRENT_BINARY_DIR}/DetoursConfigVersion.cmake.in"
#   VERSION ${Detours_VERSION}
#   COMPATIBILITY AnyNewerVersion
#   )

# configure_file(
#   "${CMAKE_CURRENT_BINARY_DIR}/DetoursConfigVersion.cmake.in"
#   "${PROJECT_BINARY_DIR}/DetoursConfigVersion.cmake" @ONLY)

# configure_file(
#   DetoursConfig.cmake.in
#   "${PROJECT_BINARY_DIR}/DetoursConfig.cmake" @ONLY)


## INSTALL
include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

install(
  TARGETS detours
  EXPORT DetoursInstallTargets
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib)

install(
  FILES ${DETOURS_HEADERS}
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/detours)

set(DETOURS_INSTALL_INCLUDE_DIR ${CMAKE_INSTALL_INCLUDEDIR}/detours)

target_include_directories(detours PUBLIC
  $<BUILD_INTERFACE:${DETOURS_INCLUDE_DIR}>
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

install(EXPORT DetoursInstallTargets NAMESPACE Detours:: DESTINATION ${CMAKE_INSTALL_PREFIX}/share)

write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/InstallDetoursConfigVersion.cmake.in"
  VERSION ${Detours_VERSION}
  COMPATIBILITY AnyNewerVersion
  )

configure_file(
  "${CMAKE_CURRENT_BINARY_DIR}/InstallDetoursConfigVersion.cmake.in"
  "${CMAKE_CURRENT_BINARY_DIR}/DetoursConfigVersion.cmake" @ONLY)

configure_file(
  cmake/DetoursConfig.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/DetoursConfig.cmake" @ONLY)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/DetoursConfig.cmake ${CMAKE_CURRENT_BINARY_DIR}/DetoursConfigVersion.cmake DESTINATION ${CMAKE_INSTALL_DATADIR})
