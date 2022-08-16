###########################################################
# -*- mode: CMake -*-
#
# Copyright (c) ******
# All rights reserved.
#
# This file is distributed under the *********
# License.  See the file COPYING for details.
###########################################################

cmake_minimum_required(VERSION 3.16.3)

project(PROJECT VERSION 1.0)


###########################################################
# Set a default build type
#
# Release - Adds the -O3 -DNDEBUG flags to the compiler
# Debug - Adds the -g flag
# MinSizeRel - Adds -Os -DNDEBUG
# RelWithDebInfo - Adds -O2 -g -DNDEBUG flags
###########################################################

# set the C++ standard to C++ 11
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Set a default build type if none was specified
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    message("Setting build type to 'RelWithDebInfo' as none was specified.")
    set(CMAKE_BUILD_TYPE Debug CACHE STRING "Choose the type of build." FORCE)
    # Set the possible values of build type for cmake-gui
    set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()

set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wall")


############################################################
# get library
############################################################

# openGL
find_package(OpenGL REQUIRED)
message(STATUS "OpenGL included at ${OpenGL_INCLUDE_DIR}")
find_package(Freetype REQUIRED)
message(STATUS "OpenGL included at ${Freetype_INCLUDE_DIR}")

# Submodule
find_package(Git QUIET)
if(GIT_FOUND AND EXISTS "${CMAKE_SOURCE_DIR}/.git")
    # Update submodules as needed
    option(GIT_SUBMODULE "Check submodules during build" ON)
    if(GIT_SUBMODULE)
        message(STATUS "Submodule update")
        execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} RESULT_VARIABLE GIT_SUBMOD_RESULT)
        if(NOT GIT_SUBMOD_RESULT EQUAL "0")
            message(FATAL_ERROR "git submodule update --init faileed with ${GIT_SUBMOD_RESULT}, please checkout submodules")
        endif()
    endif()
endif()
 
# submodule glfw
if(NOT EXISTS "${CMAKE_SOURCE_DIR}/external/glfw/CMakeLists.txt")
    message(FATAL_EOORO "The glfw submodules was not downloaded! GIT_SUBMODULE was turned off or failed. Please")
endif()
# submodule glm
if(NOT EXISTS "${CMAKE_SOURCE_DIR}/external/glm/CMakeLists.txt")
    message(FATAL_EOORO "The glm submodules was not downloaded! GIT_SUBMODULE was turned off or failed. Please")
endif()

add_subdirectory(${CMAKE_SOURCE_DIR}/external/glfw)
add_subdirectory(${CMAKE_SOURCE_DIR}/external/glad)
add_subdirectory(${CMAKE_SOURCE_DIR}/external/stb)
add_subdirectory(${CMAKE_SOURCE_DIR}/external/glm)

############################################################
# Create an executable
############################################################

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/build)
file(COPY "${CMAKE_SOURCE_DIR}/data" DESTINATION "${CMAKE_CURRENT_BINARY_DIR}")

file(GLOB SOURCES ${CMAKE_SOURCE_DIR}/src/*.cpp)

add_executable(${PROJECT_NAME} ${SOURCES})

target_include_directories(${PROJECT_NAME}
    PUBLIC ${CMAKE_SOURCE_DIR}/include
    PUBLIC ${CMAKE_BINARY_DIR} # for configure file
    PUBLIC /usr/include/freetype2
)

target_link_libraries(${PROJECT_NAME}
    OpenGL::GL
    glfw
    glad
    glm
    stb
    ${FREETYPE_LIBRARIES}
)