cmake_minimum_required(VERSION 3.12)
project(lua-cmake VERSION 1.0.1 DESCRIPTION "Lua build for CMake with FetchContent")

option(BUILD_SHARED_LIBS "Build as shared library" ON)
option(LUA_TAG "The tag to use for Lua" "v5.4.6")

if (WIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
endif()

include(FetchContent)
FetchContent_Declare(luasource
    GIT_REPOSITORY https://github.com/lua/lua
    GIT_TAG ${LUA_TAG})
FetchContent_MakeAvailable(luasource)


file(GLOB SRC_LIBLUA "${luasource_SOURCE_DIR}/*.c")
set (CMAKE_C_STANDARD 99)


# We'll exclude the interpreter and compiler because I'm lazy :D 
# These could be included, but they'd have to be separate and built with add_executable, and realistically, people using this project
# aren't going to use it for the executables.
set(LUAEXEC ${luasource_SOURCE_DIR}/lua.c ${luasource_SOURCE_DIR}/luac.c ${luasource_SOURCE_DIR}/onelua.c)
list(REMOVE_ITEM SRC_LIBLUA ${LUAEXEC})

add_library(lua ${SRC_LIBLUA})

target_compile_definitions(lua
    PRIVATE
    $<$<PLATFORM_ID:Linux>:LUA_USE_LINUX>)

# Wtf is this for?
target_compile_options(lua
    PRIVATE
    $<$<OR:$<C_COMPILER_ID:AppleClang>,$<C_COMPILER_ID:Clang>,$<C_COMPILER_ID:GNU>>:
    -Wextra -Wshadow -Wsign-compare -Wundef -Wwrite-strings -Wredundant-decls
    -Wdisabled-optimization -Waggregate-return -Wdouble-promotion -Wdeclaration-after-statement
    -Wmissing-prototypes -Wnested-externs -Wstrict-prototypes -Wc++-compat -Wold-style-definition>)

add_library(lua-header
    INTERFACE)

# This is lazy as fuck
target_include_directories(lua-header
    INTERFACE
    ${luasource_SOURCE_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/include/)

target_link_libraries(lua
    INTERFACE
    lua-header)

add_library(lua::lib ALIAS lua)
add_library(lua::header ALIAS lua-header)

add_subdirectory(test EXCLUDE_FROM_ALL)
