cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

# ---- Project ----

# Note: update this to your new project's name and version
project(
        App
        VERSION 1.0
        LANGUAGES CXX
)

# ---- Add dependencies via CPM ----
# see https://github.com/TheLartians/CPM.cmake for more info

include(../cmake/tools.cmake)
include(../cmake/CPM.cmake)

# PackageProject.cmake will be used to make our target installable
CPMAddPackage("gh:TheLartians/PackageProject.cmake@1.4.1")

# ---- Add source files ----

# Note: globbing sources is considered bad practice as CMake's generators may not detect new files
# automatically. Keep that in mind when changing files, or explicitly mention them here.
file(GLOB_RECURSE headers CONFIGURE_DEPENDS "${PROJECT_SOURCE_DIR}/include/*.h")
file(GLOB_RECURSE sources CONFIGURE_DEPENDS "${PROJECT_SOURCE_DIR}/source/*.cpp")

# ---- Conan ----

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup(TARGETS)

# ---- Create App executable ----

add_executable(App ${headers} ${sources})

set_target_properties(App PROPERTIES CXX_STANDARD 17)

# being a cross-platform target, we enforce standards conformance on MSVC
target_compile_options(App PUBLIC "$<$<COMPILE_LANG_AND_ID:CXX,MSVC>:/permissive>")

target_include_directories(
        App PRIVATE $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
        App PRIVATE $<BUILD_INTERFACE:${libGreeter_SOURCE_DIR}/include>
)

# Link dependencies
target_link_libraries(App PUBLIC sub::libGreeter CONAN_PKG::fmt CONAN_PKG::cxxopts)

# ---- Create an installable target ----
# this allows users to install and find the library via `find_package()`.

# the location where the project's version header will be placed should match the project's regular
# header paths
#string(TOLOWER ${PROJECT_NAME}/version.h VERSION_HEADER_LOCATION)
#
#packageProject(
#        NAME ${PROJECT_NAME}
#        VERSION ${PROJECT_VERSION}
##        NAMESPACE ${PROJECT_NAME}
#        BINARY_DIR ${PROJECT_BINARY_DIR}
#        INCLUDE_DESTINATION include/${PROJECT_NAME}-${PROJECT_VERSION}
#        VERSION_HEADER "${VERSION_HEADER_LOCATION}"
#        COMPATIBILITY SameMajorVersion
#)
