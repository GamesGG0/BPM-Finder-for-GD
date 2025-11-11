cmake_minimum_required(VERSION 3.21)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
if ("${CMAKE_SYSTEM_NAME}" STREQUAL "iOS" OR IOS)
    set(CMAKE_OSX_ARCHITECTURES "arm64")
else()
    set(CMAKE_OSX_ARCHITECTURES "arm64;x86_64")
endif()
set(CMAKE_CXX_VISIBILITY_PRESET hidden)

project(BPMFinder VERSION 1.0.0)

file(GLOB_RECURSE SOURCES CONFIGURE_DEPENDS src/*.cpp)

# Add MiniBPM sources (change this from BTrack)
file(GLOB MINIBPM_SOURCES libs/minibpm/*.cpp)

message("MINIBPM SOURCES: ${MINIBPM_SOURCES}")

add_library(${PROJECT_NAME} SHARED ${SOURCES} ${MINIBPM_SOURCES})

if (NOT DEFINED ENV{GEODE_SDK})
    message(FATAL_ERROR "Unable to find Geode SDK! Please define GEODE_SDK environment variable to point to Geode")
else()
    message(STATUS "Found Geode: $ENV{GEODE_SDK}")
endif()

add_subdirectory($ENV{GEODE_SDK} ${CMAKE_CURRENT_BINARY_DIR}/geode)

# Add include directories for MiniBPM and minimp3
target_include_directories(${PROJECT_NAME} PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/libs/MiniBpm
    ${CMAKE_CURRENT_SOURCE_DIR}/libs/minimp3
)

setup_geode_mod(${PROJECT_NAME})
