cmake_minimum_required(VERSION 3.15 FATAL_ERROR)
project(ZeekPlugin_Fluvio CXX C)
include(ZeekPlugin)

file(STRINGS "UPSTREAM_VERSIONS" _lines)
foreach(_line IN LISTS _lines)
    string(STRIP "${_line}" _line)
    if(_line MATCHES "^fluvio_client_cpp=(.+)$")
        set(FLUVIO_CLIENT_CPP_VERSION "${CMAKE_MATCH_1}")
    endif()
endforeach()
include(FetchContent)
FetchContent_Declare(
        fluvio_client_cpp
        URL https://github.com/ASTRAOS-de/fluvio-client-cpp/releases/download/v${FLUVIO_CLIENT_CPP_VERSION}/fluvio-client-cpp-linux-x64.tar.gz
)
FetchContent_MakeAvailable(fluvio_client_cpp)

# The tarball already has its own Config.cmake, so just point find_package at it
list(APPEND CMAKE_PREFIX_PATH "${fluvio_client_cpp_SOURCE_DIR}")
find_package(fluvio_client_cpp CONFIG REQUIRED)

zeek_plugin_begin(ZEEK FLUVIO)
zeek_plugin_cc(src/Plugin.cc)
zeek_plugin_cc(src/FluvioWriter.cc)
zeek_plugin_link_library(ASTRAOS::fluvio_client_cpp)
zeek_plugin_end()
