# this script adds all subprojects to a single build to allow IDEs understand the full project
# structure.

cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

project(ModernCpp)

include(./cmake/tools.cmake)

# needed to generate test target
enable_testing()

add_subdirectory(app)
add_subdirectory(Greeter)
#add_subdirectory(test)
#add_subdirectory(documentation)
