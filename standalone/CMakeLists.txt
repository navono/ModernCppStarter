cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

project(GreeterStandalone LANGUAGES CXX)

# --- Import tools ----

include(../cmake/tools.cmake)

# ---- Add source files ----

file(GLOB sources CONFIGURE_DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/source/*.cpp)

# ---- Conan ----

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup(TARGETS)

# ---- Create standalone executable ----

add_executable(GreeterStandalone ${sources})

set_target_properties(GreeterStandalone PROPERTIES CXX_STANDARD 17 OUTPUT_NAME "Greeter")

target_link_libraries(GreeterStandalone Greeter::Greeter CONAN_PKG::cxxopts)

target_include_directories(GreeterStandalone
        PUBLIC ${PROJECT_SOURCE_DIR}/include
        )