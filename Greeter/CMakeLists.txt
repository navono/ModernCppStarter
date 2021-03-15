cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

# ---- Project ----

# Note: update this to your new project's name and version
project(
        Greeter
        VERSION 0.2.1
        LANGUAGES CXX
)

# Call configure files on ver.h.in to set the version.
# Uses the standard ${VARIABLE} syntax in the file
#configure_file(source/version.h.in ${PROJECT_SOURCE_DIR}/include/${PROJECT_NAME}/version.h)

# ---- Add dependencies via CPM ----
# see https://github.com/TheLartians/CPM.cmake for more info

include(../cmake/tools.cmake)
include(../cmake/CPM.cmake)

# PackageProject.cmake will be used to make our target installable
#CPMAddPackage("gh:TheLartians/PackageProject.cmake@1.4.1")


# Note: globbing sources is considered bad practice as CMake's generators may not detect new files
# automatically. Keep that in mind when changing files, or explicitly mention them here.
file(GLOB_RECURSE headers CONFIGURE_DEPENDS "${PROJECT_SOURCE_DIR}/include/*.h")
file(GLOB_RECURSE sources CONFIGURE_DEPENDS "${PROJECT_SOURCE_DIR}/source/*.cpp")

# ---- Conan ----

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup(TARGETS)

# ---- Create library ----

# Note: for header-only libraries change all PUBLIC flags to INTERFACE and create an interface
# target: add_library(Greeter INTERFACE)
add_library(${PROJECT_NAME} ${headers} ${sources})
add_library(sub::Greeter ALIAS ${PROJECT_NAME})

target_link_libraries(${PROJECT_NAME} PUBLIC CONAN_PKG::fmt)

target_include_directories(
        ${PROJECT_NAME} PUBLIC $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include> PRIVATE sources
)

string(TOLOWER ${PROJECT_NAME}/version.h VERSION_HEADER_LOCATION)

packageProject(
        NAME ${PROJECT_NAME}
        VERSION ${PROJECT_VERSION}
        NAMESPACE ${PROJECT_NAME}
        BINARY_DIR ${PROJECT_BINARY_DIR}
        INCLUDE_DIR ../lib/include
        INCLUDE_DESTINATION include/${PROJECT_NAME}-${PROJECT_VERSION}
        VERSION_HEADER "${VERSION_HEADER_LOCATION}"
        COMPATIBILITY SameMajorVersion
)
