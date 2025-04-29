cmake_minimum_required(VERSION 3.10)

include(GNUInstallDirs)

set(name papki)

project(${name})

if(NOT TARGET utki::utki)
find_package(utki CONFIG REQUIRED)
endif()

find_package(ZLIB REQUIRED)

set(srcdir "${CMAKE_CURRENT_SOURCE_DIR}/../src/${name}")
file(GLOB_RECURSE src RELATIVE "${srcdir}" CONFIGURE_DEPENDS "${srcdir}/*.cpp" "${srcdir}/*.hpp")

set(srcs)
foreach(file ${src})
    get_filename_component(path "${file}" DIRECTORY)
    string(REPLACE "/" "\\" path "Source Files/${path}")
    source_group("${path}" FILES "${srcdir}/${file}")
    list(APPEND srcs "${srcdir}/${file}")
endforeach()

set(deps_srcdir "${CMAKE_CURRENT_SOURCE_DIR}/../src_deps")
file(GLOB_RECURSE deps_src RELATIVE "${deps_srcdir}" CONFIGURE_DEPENDS "${deps_srcdir}/*.c" "${deps_srcdir}/*.h")

set(deps_srcs)
foreach(file ${deps_src})
    get_filename_component(path "${file}" DIRECTORY)
    string(REPLACE "/" "\\" path "Source Files/${path}")
    source_group("${path}" FILES "${deps_srcdir}/${file}")
    list(APPEND deps_srcs "${deps_srcdir}/${file}")
endforeach()

add_library(
    ${name}
    STATIC
    ${srcs}
    ${deps_srcs}
)

target_compile_features(${name} PUBLIC cxx_std_20)
set_target_properties(${name} PROPERTIES CXX_STANDARD_REQUIRED ON)
set_target_properties(${name} PROPERTIES CXX_EXTENSIONS OFF)

target_include_directories(
    ${name}
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../src>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../src_deps>
        $<INSTALL_INTERFACE:include>
)

target_link_libraries(
    ${name}
    PUBLIC
        utki::utki
        ZLIB::ZLIB
)

if(NOT PAPKI_DISABLE_INSTALL)

# install library header files preserving directory hierarchy
install(
    DIRECTORY
        "${CMAKE_CURRENT_SOURCE_DIR}/../src/${name}"
    DESTINATION
        "${CMAKE_INSTALL_INCLUDEDIR}"
    FILES_MATCHING PATTERN
        "*.hpp"
)

install(
    TARGETS
        ${name}
    EXPORT # generate cmake configs
        ${name}-config
)

# install cmake configs
install(
    EXPORT
        ${name}-config
    FILE
        ${name}-config.cmake
    DESTINATION
        ${CMAKE_INSTALL_DATAROOTDIR}/${name}
    NAMESPACE
        ${name}::
)

endif()
