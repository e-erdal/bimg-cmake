cmake_minimum_required(VERSION 3.21)
project(bimg)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(GNUInstallDirs)

if(NOT BIMG_DIR)
	set(BIMG_DIR "${CMAKE_CURRENT_SOURCE_DIR}/bimg" CACHE STRING "Location of bimg.")
elseif(NOT IS_ABSOLUTE "${BIMG_DIR}")
	get_filename_component(BIMG_DIR "${BIMG_DIR}" REALPATH BASE_DIR "${CMAKE_CURRENT_SOURCE_DIR}")
endif()

if (NOT BX_DIR)
    message(FATAL_ERROR "bimg-cmake requires bx-cmake.")
endif()

file(GLOB BIMG_SOURCES ${BIMG_DIR}/src/*.cpp)
file(GLOB BIMG_MINIZ_SOURCES ${BIMG_DIR}/3rdparty/tinyexr/deps/miniz/*.c)
file(GLOB BIMG_ASTCC_SOURCES ${BIMG_DIR}/3rdparty/astc-codec/src/decoder/*.cc)

add_library(bimg STATIC ${BIMG_SOURCES} ${BIMG_MINIZ_SOURCES} ${BIMG_ASTCC_SOURCES})
    target_include_directories(bimg PUBLIC 
        ${BX_DIR}/include
        ${BIMG_DIR}/include 
        ${BIMG_DIR}/3rdparty/astc-codec 
        ${BIMG_DIR}/3rdparty/astc-codec/include
        ${BIMG_DIR}/3rdparty/tinyexr/depds/miniz
    )

    if (UNIX AND NOT APPLE)
        target_compile_options(bimg PUBLIC "-fPIC")
    endif()