cmake_minimum_required(VERSION 3.12)

set(PROJ_NAME squish)
project(${PROJ_NAME} CXX)

set(CMAKE_CXX_STANDARD 17)
set(LIB_TYPE STATIC)

SET(SQUISH_SSE_MODE "NOSSE" CACHE STRING "building squish to use NOSSE or SSE or SSE2 instructions")
SET_PROPERTY(CACHE SQUISH_SSE_MODE PROPERTY STRINGS NOSSE SSE SSE2)

function(def_vs_filters FILE_LIST)
	foreach(source IN LISTS FILE_LIST)
	    get_filename_component(source_path "${source}" PATH)
	    string(REPLACE "${CMAKE_CURRENT_LIST_DIR}" "" source_path_relative "${source_path}")
	    string(REPLACE "/" "\\" source_path_msvc "${source_path_relative}")
	    source_group("${source_path_msvc}" FILES "${source}")
	endforeach()
endfunction(def_vs_filters)

IF(${SQUISH_USE_SSE} MATCHES "SSE")
    message("building squish to use SSE instructions")
    SET(SQUISH_USE_SSE 1)
	
	# TODO enable sse
	
elseif(${SQUISH_USE_SSE} MATCHES "SSE2")
    message("building squish to use SSE2 instructions")
    SET(SQUISH_USE_SSE 2)
	
	if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
		add_compile_options(/arch:SSE2)
	endif()
	
else()
    message("building squish to not use SSE instructions")
    SET(SQUISH_USE_SSE 0)
	 
	# TODO ???
	 
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

set_target_properties(${PROJ_NAME} PROPERTIES LINKER_LANGUAGE CXX)
target_include_directories(${PROJ_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)