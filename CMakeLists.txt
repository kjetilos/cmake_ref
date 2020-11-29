# A CMake file consists of "Command Invocations" these are
# names followed by parenthesis enclosed arguments separated by
# whitespace.
#
# So the major work of understanding CMake files is to understand what
# the different commands do and what they are used for.

cmake_minimum_required(VERSION 3.10)

project(cmake_test
  LANGUAGES C CXX
  VERSION 1.0)

# We can separate CMake functionality into modules that can be included like 
# this.
include(cmake/utils.cmake)

# This is how to print messages in CMake
message(STATUS "This is a status message")

# The message command contains a mode argument which effects
# execution, FATAL_ERROR and SEND_ERROR stops, while WARNING continues
message(WARNING "This is a warning message")
message(DEBUG "Debug information")
message(TRACE "Only for tracing")
message(INFO "Source directory is ${CMAKE_SOURCE_DIR}")

# Variables in the CMake file can be created using set() string() and list()
# These variables will be set in the current directory scope
set(MY_VAR "Some Value")
set(MY_VAR_2 "With ${MY_VAR}")
string(APPEND SOME_VAR " something else")

cmake_print_variables(MY_VAR)
# Here we add a subdirectory, this will introduce a subdirectory context
# and CMake will look for a CMakeLists.txt file in that subdirectory
add_subdirectory(sub)
message("Back into parent directory scope")
cmake_print_variables(MY_SUB_VAR)
if(NOT "${MY_SUB_VAR}")
  message("MY_SUB_VAR is not set in the parent scope")
endif()

# unset a variable either explicitly or by providing zero arguments
set(MY_VAR)
unset(MY_VAR_2)

message("Environment variable LANG=$ENV{LANG}")

# Here we set a Cache entry. This is a config option which appears in the 
# CMakeCache.txt file. It will have global scope and can be modified by using
# cmake-gui or by editing the cache file.
set(NAME_CONFIG "My name" CACHE STRING "Input name")
message("Hello ${NAME_CONFIG}")

# Lists are create using set() as well with space as separator
set(MY_LIST "a" "b" "c")
set(MY_LIST ${MY_LIST} "d")
message("MY_LIST=${MY_LIST}")

list(APPEND MY_LIST "e" "f")


# CMake has a concept of build type which is specified using
# the CMAKE_BUILD_TYPE configuration. However this is by default
# the empty string. The following code will set the default to Debug
# if nothing is provided
set(default_build_type "Release")

# This code will set the CMAKE_BUILD_TYPE value in the Cache and provide 
# metadata to the entry so that cmake-gui can select between Debug and Release.
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  message(STATUS "Setting build type to ${default_build_type}")
  set(CMAKE_BUILD_TYPE "${default_build_type}" CACHE
      STRING "Chose the type of build." FORCE)
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
               "Debug" "Release")
endif()


add_executable(app
  src/main.c)

# Here we add a macro definition to all the build targets
add_definitions(-DMAX_COUNT=256)

# If we want to add a macro to a single target we use 
# the target specific command like target_compile_definitions()
target_compile_definitions(app
  PRIVATE
    NUM_BUFFERS=32
)

if(${DEMO_ENABLE})
  add_executable(demo
    src/demo.c)
endif()

