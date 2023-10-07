add_executable(lua-cmake-cpp-tests
    src/cpp/IncludeTests.cpp
    src/cpp/CStyleIncludes.cpp
)
add_executable(lua-cmake-c-tests
    src/c/IncludeTests.c
)

target_link_libraries(lua-cmake-c-tests PUBLIC lua::lib m)
target_link_libraries(lua-cmake-cpp-tests PUBLIC lua::lib)
