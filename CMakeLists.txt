cmake_minimum_required (VERSION 3.15)
cmake_policy(VERSION 3.15)

set(TARGET_NAME "k")

project(${TARGET_NAME} VERSION 1.0
          DESCRIPTION "K project"
          LANGUAGES C CXX)

# Books: 
# Help : https://cmake.org/cmake/help/latest/
# Tutorial : https://cmake.org/cmake/help/latest/guide/tutorial/index.html
# An Introduction to Modern CMake : https://cliutils.gitlab.io/modern-cmake/

# Picking a compiler
# SHELL> CC=clang CXX=clang++ cmake ..

# If you are using a command line build tool like make, you can get verbose builds:
# SHELL> VERBOSE=1 make

# These are common CMake options to most packages:
# -DCMAKE_BUILD_TYPE= Pick from Release, RelWithDebInfo, Debug, or sometimes more.
# -DCMAKE_INSTALL_PREFIX= The location to install to. System install on UNIX would often be /usr/local (the default), user directories are often ~/.local, or you can pick a folder.
# -DBUILD_SHARED_LIBS= You can set this ON or OFF to control the default for shared libraries (the author can pick one vs. the other explicitly instead of using the default, though)
# -DBUILD_TESTING= This is a common name for enabling tests, not all packages use it, though, sometimes with good reason.

# Debug CMake configure
# cmake --trace -G Ninja ..  # print every line of CMake that is run
# --trace-source="filename", which will print out every executed line of just the file you are interested in when it runs.

# Local Variables
# set(MY_VARIABLE "value")
# set(MY_LIST "one" "two")
# set(MY_LIST "one;two")

# Environment variables
# set(ENV{variable_name} value) 
# and get $ENV{variable_name}

# Control flow
# if(variable)
#     # If variable is `ON`, `YES`, `TRUE`, `Y`, or non zero number
# else()
#     # If variable is `0`, `OFF`, `NO`, `FALSE`, `N`, `IGNORE`, `NOTFOUND`, `""`, or ends in `-NOTFOUND`
# endif()
# Unary: NOT, TARGET, EXISTS (file), DEFINED, etc.
# Binary: STREQUAL, AND, OR, MATCHES (regular expression), VERSION_LESS, VERSION_LESS_EQUAL (CMake 3.7+), etc.

# Macros and Functions
# set the variables you want visible to the outside world to PARENT_SCOPE
# function(SIMPLE REQUIRED_ARG)
#     message(STATUS "Simple arguments: ${REQUIRED_ARG}, followed by ${ARGV}")
#     set(${REQUIRED_ARG} "From SIMPLE" PARENT_SCOPE)
# endfunction()
# simple(This)
# message("Output: ${This}")

# Arguments
# function(COMPLEX)
#     cmake_parse_arguments(
#         COMPLEX_PREFIX
#         "SINGLE;ANOTHER"
#         "ONE_VALUE;ALSO_ONE_VALUE"
#         "MULTI_VALUES"
#         ${ARGN}
#     )
# endfunction()
# complex(SINGLE ONE_VALUE value MULTI_VALUES some other values)
# Inside the function after this call, you'll find:
# COMPLEX_PREFIX_SINGLE = TRUE
# COMPLEX_PREFIX_ANOTHER = FALSE
# COMPLEX_PREFIX_ONE_VALUE = "value"
# COMPLEX_PREFIX_ALSO_ONE_VALUE = <UNDEFINED>
# COMPLEX_PREFIX_MULTI_VALUES = "some;other;values"


set (BUILD_TYPE ${CMAKE_BUILD_TYPE})
if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug)
    set(BUILD_TYPE Debug)
endif ()

if( CMAKE_BUILD_TYPE STREQUAL "Debug" )
    message( "Debug Build >>>" )
    add_definitions(-DDEBUG)
    add_definitions(-DDebug)
endif()

# Export compile commands
set( CMAKE_EXPORT_COMPILE_COMMANDS 1 ) 

set (CMAKE_C_STANDARD 11)
set (CMAKE_CXX_STANDARD 20)
set (CMAKE_MACOSX_RPATH TRUE)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake)

# Detect linux
if (UNIX AND NOT APPLE)
    set(LINUX TRUE)
endif ()

message(STATUS "Running cmake version ${CMAKE_VERSION}")

# disable all warnings
# add_compile_options( -w  )

if (APPLE) 
	message(STATUS "Disable deprecated warnings")
	add_compile_options( -Wno-deprecated)
endif ()

# standard include directory
include_directories("/usr/local/include" "/usr/include")


# See modules: /usr/local/Cellar/cmake/*/share/cmake/Modules
include(CheckIncludeFileCXX)
include(CheckCXXSourceCompiles)
include(CheckCXXCompilerFlag)


# Check compiler flags
# check_cxx_compiler_flag("-std=c++17" CXX17_FLAG)
# if (CXX17_FLAG)
#     set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++17")
# endif()


# Check cxx source can complie. CMAKE_REQUIRED_FLAGS : Additional flags to pass to the compiler.
set(CMAKE_REQUIRED_FLAGS "${CMAKE_CXX_FLAGS}")
check_cxx_source_compiles(
   "#include <mutex>
    int main () {return 0;}
	"
  HAVE_MUTEX_HEADER
)
set(CMAKE_REQUIRED_FLAGS "")


#------------------------------------- libasio -----------------------------------------#
# macOS: brew install asio
# Linux: apt install libasio-dev
# set (CMAKE_REQUIRED_INCLUDES "/usr/include" "/usr/local/include")
# CHECK_INCLUDE_FILE_CXX(asio.hpp HAVE_ASIO_H)
# if (NOT HAVE_ASIO_H)
#    message(FATAL_ERROR "===>>>> NOT Found libasio! ======")
# endif(NOT HAVE_ASIO_H)
# set (CMAKE_REQUIRED_INCLUDES "")

#------------------------------------- sqlite -----------------------------------------#
# set (SQLITE3_SRC_LIST 
# 	"libs/sqlite3/sqlite3.c"
# 	)
# add_library(sqlite3 STATIC ${SQLITE3_SRC_LIST})

#------------------------------------- minizip -----------------------------------------#
# add_subdirectory(libs/minizip EXCLUDE_FROM_ALL)

#------------------------------------- Boost -----------------------------------------#
# Linux: sudo apt install libboost-all-dev 
# Mac: brew install boost
# find_package( Boost 1.58 REQUIRED COMPONENTS atomic chrono context coroutine date_time exception filesystem graph iostreams locale log program_options random regex serialization signals timer system thread wave )
# find_package( Boost 1.69 REQUIRED COMPONENTS filesystem locale)
# include_directories( ${Boost_INCLUDE_DIR} )

#------------------------------------- OpenSSL ----------------------------------------#
# Linux: sudo apt install libssl-dev
# Mac: brew install openssl
#      echo 'export PATH="/usr/local/opt/openssl/bin:$PATH"' >> ~/.bash_profile
#if(${CMAKE_SYSTEM_NAME} STREQUAL "Darwin")
#	set (OPENSSL_ROOT_DIR "/usr/local/opt/openssl" )
#	message(STATUS ">>> ${CMAKE_SYSTEM_NAME} uses OpenSSL: ${OPENSSL_ROOT_DIR}")
#endif()
#find_package( Openssl REQUIRED )
#include_directories( ${OPENSSL_INCLUDE_DIR} )

#------------------------------------- BZip2 ----------------------------------------#
#find_package( BZip2 REQUIRED)
#include_directories( ${BZIP2_INCLUDE_DIR} )

#------------------------------------- CURL ----------------------------------------#
#find_package( CURL REQUIRED)
#include_directories( ${CURL_INCLUDE_DIR} )

#------------------------------------- project header and lib path -----------------#
# Other external source headers
# include_directories( ${CMAKE_CURRENT_SOURCE_DIR}/external/include )
# link_directories( ${CMAKE_CURRENT_SOURCE_DIR}/external/lib )
# include_directories( "libs/sqlite3" )
# include_directories( "libs/minizip/src" )
# include_directories( "libs/mariadb-connector-c/include" )
# include_directories( ${CMAKE_BINARY_DIR}/libs/mariadb-connector-c/include/ )


#------------------------------------- Source File List ----------------------------------------#
# set(client_SRC_LIST 1.cpp 2.hpp 3.h 4.cxx ) # add files manually
# Not include subfolder, use file(GLOB srclist "*.cpp" "*.h")
# file(GLOB COMMON_SRC_LIST
# 	"common/*.hpp"
# 	"common/*.h"
# 	"common/*.cc"
# 	"common/*.cpp"
# 	"common/*.cxx"
# 	)
# file(GLOB_RECURSE SRC_LIST
# 	"src/*.hpp" 
# 	"src/*.h"
# 	"src/*.cpp" 
# 	"src/*.cc" 
# 	"src/*.cxx"
# 	)

#------------------------------------- DEP Libs ----------------------------------------#
set (DEP_LIBS 
	# ${Boost_LIBRARIES} 
	# ${OPENSSL_LIBRARIES} 
	# ${BZIP2_LIBRARIE}
	# ${CURL_LIBRARIE}
	)

#------------------------------------- K ----------------------------------------#
set(SRC_LIST
	main.cpp
	)
add_executable(${TARGET_NAME} ${SRC_LIST} )
target_link_libraries (${TARGET_NAME} ${DEP_LIBS})
