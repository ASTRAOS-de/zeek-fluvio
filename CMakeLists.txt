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
        GIT_REPOSITORY https://github.com/ASTRAOS-de/fluvio-client-cpp.git
        GIT_TAG v${FLUVIO_CLIENT_CPP_VERSION}
)

FetchContent_MakeAvailable(fluvio_client_cpp)

get_target_property(FLUVIO_INC ASTRAOS::fluvio_client_cpp INTERFACE_INCLUDE_DIRECTORIES)
include_directories(BEFORE ${FLUVIO_INC})

zeek_plugin_begin(ZEEK FLUVIO)
zeek_plugin_cc(src/Plugin.cc)
zeek_plugin_cc(src/FluvioWriter.cc)
zeek_plugin_link_library(ASTRAOS::fluvio_client_cpp)
zeek_plugin_end()
