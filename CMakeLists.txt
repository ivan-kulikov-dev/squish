cmake_minimum_required(VERSION 3.12)

set(PROJ_NAME squish)
project(${PROJ_NAME} CXX)

set(CMAKE_CXX_STANDARD 17)
set(LIB_TYPE STATIC)

option(SQUISH_ALTIVEC "Enable building squish to use Altivec instructions." OFF)
SET(SQUISH_SSE_MODE "NOSSE" CACHE STRING "building squish to use NOSSE or SSE or SSE2 instructions")
SET_PROPERTY(CACHE SQUISH_SSE_MODE PROPERTY STRINGS NOSSE SSE SSE2)

IF(SQUISH_ALTIVEC)
     SET(SQUISH_USE_ALTIVEC 1)
ENDIF()

IF(${SQUISH_USE_SSE} MATCHES "SSE")
     message("building squish to use SSE instructions")
     SET(SQUISH_USE_SSE "1")
elseif(${SQUISH_USE_SSE} MATCHES "SSE2")
     message("building squish to use SSE2 instructions")
     SET(SQUISH_USE_SSE "2")
else()
     message("building squish to not use SSE instructions")
     SET(SQUISH_USE_SSE "0")
ENDIF()

configure_file("${PROJECT_SOURCE_DIR}/config.h.in" "${PROJECT_BINARY_DIR}/config.h" @ONLY)

include_directories(${CMAKE_CURRENT_BINARY_DIR})

file(GLOB_RECURSE SRC_FILES
    "${CMAKE_CURRENT_LIST_DIR}/src/*.cpp"
    "${CMAKE_CURRENT_LIST_DIR}/include/*.h"
)
def_vs_filters("${SRC_FILES}")
add_library(${PROJ_NAME} ${LIB_TYPE} ${SRC_FILES})
if(WIN32)
#	target_compile_options(${PROJ_NAME} PRIVATE /wd4251)
#	target_compile_options(${PROJ_NAME} PRIVATE /wd4996)
endif()


