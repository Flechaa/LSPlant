cmake_minimum_required(VERSION 3.4.1)
project(lsplant)

find_program(CCACHE ccache)

if (CCACHE)
    set(CMAKE_CXX_COMPILER_LAUNCHER ${CCACHE})
    set(CMAKE_C_COMPILER_LAUNCHER ${CCACHE})
endif ()

if (LSPLANT_STANDALONE)
    find_package(cxx REQUIRED CONFIG)
    link_libraries(cxx::cxx)
endif()

add_definitions(-std=c++20)

set(SOURCES lsplant.cc)

set(DEX_BUILDER_BUILD_SHARED OFF CACHE INTERNAL "" FORCE)
add_subdirectory(external/dex_builder)

add_library(${PROJECT_NAME}_static STATIC ${SOURCES})
target_include_directories(${PROJECT_NAME}_static PUBLIC include)
target_include_directories(${PROJECT_NAME}_static PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})

if (NOT DEFINED DEBUG_SYMBOLS_PATH)
    set(DEBUG_SYMBOLS_PATH ${CMAKE_BINARY_DIR}/symbols)
endif()

target_link_libraries(${PROJECT_NAME}_static PRIVATE dex_builder_static)
target_link_libraries(${PROJECT_NAME}_static PUBLIC log)
