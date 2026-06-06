cmake_minimum_required(VERSION 3.10)
project(hello_world_application)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(hello_world ${CMAKE_CURRENT_SOURCE_DIR}/hello_world.cpp)
target_link_libraries(hello_world formatter_ex)
