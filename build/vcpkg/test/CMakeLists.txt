cmake_minimum_required(VERSION 3.10)

set(CMAKE_TOOLCHAIN_FILE $ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake)

project(test)

find_package(utki CONFIG REQUIRED)
find_package(papki CONFIG REQUIRED)
find_package(ZLIB REQUIRED)

add_executable(test main.cpp)

target_link_libraries(test PRIVATE papki::papki)
