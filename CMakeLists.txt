cmake_minimum_required(VERSION 3.4)

project(banking)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_TESTS "Build tests" OFF)
option(ENABLE_COVERAGE "Enable coverage reporting" OFF)

add_subdirectory(banking)

if(ENABLE_COVERAGE)
    # Добавляем флаги покрытия для всего проекта
    add_compile_options(-O0 -g --coverage -fprofile-arcs -ftest-coverage)
    add_link_options(--coverage)
endif()

if(BUILD_TESTS)
  enable_testing()
  find_package(GTest REQUIRED)
  find_package(Threads REQUIRED)
  file(GLOB TEST_SOURCES tests/*.cpp)
  add_executable(check ${TEST_SOURCES})
  target_link_libraries(check banking gtest gtest_main gmock gmock_main Threads::Threads)
  add_test(NAME check COMMAND check)
endif()
