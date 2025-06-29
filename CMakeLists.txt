# CMakeLists.txt --- CMake project settings
##############################################################################

# CMake minimum version
cmake_minimum_required(VERSION 2.10)

# set new policy
cmake_policy(SET CMP0054 NEW)

# project name and languages
project(MsgCrack CXX RC)

# set output directory (build/)
set(CMAKE_BINARY_DIR ${CMAKE_SOURCE_DIR}/build)
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR})
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR})

# include directories
include_directories(./include)

# Unicode support
add_definitions(-DUNICODE -D_UNICODE)

# do statically link
if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    # using Clang
    SET(CMAKE_C_FLAGS "-static -s")
    SET(CMAKE_CXX_FLAGS "-static -s")
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    # using GCC
    SET(CMAKE_C_FLAGS "-static -static-libgcc -static-libstdc++ -s")
    SET(CMAKE_CXX_FLAGS "-static -static-libgcc -static-libstdc++ -s")
elseif (MSVC)
    # replace "/MD" with "/MT" (building without runtime DLLs)
    set(CompilerFlags
        CMAKE_C_FLAGS
        CMAKE_C_FLAGS_DEBUG
        CMAKE_C_FLAGS_RELEASE
        CMAKE_C_FLAGS_RELWITHDEBINFO
        CMAKE_CXX_FLAGS
        CMAKE_CXX_FLAGS_DEBUG
        CMAKE_CXX_FLAGS_RELEASE
        CMAKE_CXX_FLAGS_RELWITHDEBINFO)
    foreach(CompilerFlags ${CompilerFlags})
        string(REPLACE "/MD" "/MT" ${CompilerFlags} "${${CompilerFlags}}")
    endforeach()
endif()

##############################################################################

# MsgCrack.exe
add_executable(MsgCrack WIN32 MsgCrack.cpp MsgCrack_res.rc)
target_link_libraries(MsgCrack comctl32)
