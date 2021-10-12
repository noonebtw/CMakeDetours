cmake_minimum_required(VERSION 3.12)

project(Detours CXX)
set(Detours_VERSION 4.0.1)

set(CMAKE_EXPORT_COMPILE_COMMANDS 1)


set(DETOURS_ROOT_DIR ${CMAKE_SOURCE_DIR}/Detours)
set(DETOURS_SOURCE_DIR ${CMAKE_SOURCE_DIR}/Detours/src)

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

target_compile_definitions(detours PRIVATE
  WIN32_LEAN_AND_MEAN
  _WIN32_WINNT=0x501
  )

#target_link_libraries(detours PRIVATE libcmt)

set(DETOURS_INCLUDE_DIR ${CMAKE_CURRENT_BINARY_DIR}/include/)

foreach(f IN LISTS DETOURS_HEADERS)
  get_filename_component(header ${f} NAME)
  configure_file(${f} ${DETOURS_INCLUDE_DIR}/detours/${header} COPYONLY)
endforeach()

set_target_properties(detours PROPERTIES
  INTERFACE_INCLUDE_DIRECTORIES ${DETOURS_INCLUDE_DIR})

export(TARGETS detours NAMESPACE Detours:: FILE ${PROJECT_BINARY_DIR}/DetoursTargets.cmake)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/DetoursConfigVersion.cmake.in"
  VERSION ${Detours_VERSION}
  COMPATIBILITY AnyNewerVersion
  )

configure_file(
  "${CMAKE_CURRENT_BINARY_DIR}/DetoursConfigVersion.cmake.in"
  "${PROJECT_BINARY_DIR}/DetoursConfigVersion.cmake" @ONLY)

configure_file(
  DetoursConfig.cmake.in
  "${PROJECT_BINARY_DIR}/DetoursConfig.cmake" @ONLY)
