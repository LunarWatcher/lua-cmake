add_executable(lua-cmake-cpp-tests
    src/cpp/IncludeTests.cpp
    src/cpp/CStyleIncludes.cpp
)
add_executable(lua-cmake-c-tests
    src/c/IncludeTests.c
)

target_link_libraries(c-tests PUBLIC lua::lib m)
target_link_libraries(cpp-tests PUBLIC lua::lib)

add_custom_target(test-lua-cmake-lib
    DEPENDS lua-cmake-c-tests lua-cpp-tests
)
