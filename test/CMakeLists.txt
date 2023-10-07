add_executable(cpp-tests
    src/cpp/IncludeTests.cpp
    src/cpp/CStyleIncludes.cpp
)
add_executable(c-tests
    src/c/IncludeTests.c
)

target_link_libraries(c-tests PUBLIC lua::lib m)
target_link_libraries(cpp-tests PUBLIC lua::lib)

add_custom_target(test
    DEPENDS c-tests cpp-tests
)
