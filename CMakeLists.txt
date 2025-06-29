# CMakeLists.txt --- CMake project settings
##############################################################################

# CMake minimum version
cmake_minimum_required(VERSION 2.10)

# set new policy
cmake_policy(SET CMP0054 NEW)

# project name and language
#project(MsgCrack C)
project(MsgCrack CXX)

# set output directory (build/)
set(CMAKE_BINARY_DIR ${CMAKE_SOURCE_DIR}/build)
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR})
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR})

##############################################################################

# include directories
include_directories(../../include)

# Unicode support
add_definitions(-DUNICODE -D_UNICODE)

# set source files
set(SOURCES MsgCrack.cpp)

# Win32 or not?
if (CMAKE_HOST_WIN32)
    # enable Win32 resource
    ENABLE_LANGUAGE(RC)

    # Win32
    add_executable(MsgCrack WIN32 ${SOURCES} MsgCrack_res.rc)
else()
    message(FATAL_ERROR "To build this project, use Win32 C++ compiler")
endif()

# link
target_link_libraries(MsgCrack comctl32)

##############################################################################
# do statically link

set_target_properties(MsgCrack PROPERTIES LINK_DEPENDS_NO_SHARED 1)
set_target_properties(MsgCrack PROPERTIES LINK_SEARCH_START_STATIC 1)
set_target_properties(MsgCrack PROPERTIES LINK_SEARCH_END_STATIC 1)

set(CMAKE_FIND_LIBRARY_SUFFIXES ".a")
set(BUILD_SHARED_LIBRARIES OFF)

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
