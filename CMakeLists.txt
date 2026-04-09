cmake_minimum_required(VERSION 3.15)
project(libcxxcanard)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

find_package(ament_cmake REQUIRED)

file(GLOB_RECURSE SOURCES "cyphal/*.c" "cyphal/*.cpp")

if(DEFINED ENV{<COMMON_LIBS_DIR>})
    set(COMMON_LIBS_DIR $ENV{COMMON_LIBS_DIR})
else()
    get_filename_component(COMMON_LIBS_DIR
            "./libs"
            ABSOLUTE)
endif()
message(STATUS "${CMAKE_CURRENT_LIST_DIR} | Using <${COMMON_LIBS_DIR}> as directory of common libs")

add_subdirectory(${COMMON_LIBS_DIR}/libcanard ${PROJECT_BINARY_DIR}/build/libcanard)
add_subdirectory(${COMMON_LIBS_DIR}/o1heap ${PROJECT_BINARY_DIR}/build/o1heap)

add_library(libcxxcanard ${SOURCES} $<TARGET_OBJECTS:libcanard> $<TARGET_OBJECTS:o1heap>)
add_library(libcxxcanard::libcxxcanard ALIAS libcxxcanard)

target_include_directories(libcxxcanard PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<BUILD_INTERFACE:${COMMON_LIBS_DIR}>
  $<INSTALL_INTERFACE:include>
)

target_include_directories(libcxxcanard PRIVATE
  "${CMAKE_CURRENT_SOURCE_DIR}/src"
)

install(
  TARGETS libcxxcanard
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

install(
  DIRECTORY cyphal/
  DESTINATION include/cyphal
)

install(
  DIRECTORY libs/
  DESTINATION include
)

ament_export_include_directories(include)
ament_export_libraries(libcxxcanard)

ament_package()
