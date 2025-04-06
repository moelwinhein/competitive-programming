cmake_minimum_required(VERSION 3.30)
project(TEMPLATE_PROJECT_NAME)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})

add_compile_options(-std=c++20 -Dfileio=1 -include cstddef)
add_executable(${CMAKE_PROJECT_NAME} main.cpp)
