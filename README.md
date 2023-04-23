# Easily embed lua into applications managed with CMake

```
include(FetchContent)
FetchContent_Declare(lua
    GIT_REPOSITORY https://github.com/LunarWatcher/lua-cmake)
FetchContent_MakeAvailable(lua)
...
target_link_libraries(your-lib lua::lua)
```
