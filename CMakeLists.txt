cmake_minimum_required(VERSION 3.12)
project(lua-cmake VERSION 1.0.0 DESCRIPTION "Lua build for CMake with FetchContent")

option(BUILD_SHARED_LIBS "Build as shared library" ON)
option(LUA_TAG "The tag to use for Lua" "v5.4.4")
option(LUA_CPP "Whether or not to build for C++. Note: on by default because I'm biased and more or less exclusively use C++" ON)

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

set(publicHeaderFiles lauxlib.h lua.h luaconf.h lualib.h)

add_library(lua ${SRC_LIBLUA})

target_compile_definitions(lua
    PRIVATE
    $<$<PLATFORM_ID:Linux>:LUA_USE_LINUX LUA_COMPAT_5_2>)

target_compile_options(lua
    PRIVATE
    $<$<OR:$<C_COMPILER_ID:AppleClang>,$<C_COMPILER_ID:Clang>,$<C_COMPILER_ID:GNU>>:
    -Wextra -Wshadow -Wsign-compare -Wundef -Wwrite-strings -Wredundant-decls
    -Wdisabled-optimization -Waggregate-return -Wdouble-promotion -Wdeclaration-after-statement
    -Wmissing-prototypes -Wnested-externs -Wstrict-prototypes -Wc++-compat -Wold-style-definition>)

#set(includeDir ${CMAKE_CURRENT_BINARY_DIR}/include)
#file(COPY ${publicHeaderFiles} DESTINATION ${includeDir})

add_library(lua-header
    INTERFACE)

target_include_directories(lua-header
    INTERFACE
    ${luasource_SOURCE_DIR})

target_link_libraries(lua
    INTERFACE
    lua-header)

add_library(lua::lib ALIAS lua)
add_library(lua::header ALIAS lua-header)
