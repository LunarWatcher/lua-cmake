# Easily embed lua into applications managed with CMake: FetchContent edition

This fork is meant to allow for trivial FetchContent use. Lua itself is automaticalyl downloaded with FetchContent, and this meta repo can also be downloaded with FetchContent:

```
include(FetchContent)
FetchContent_Declare(lua
    GIT_REPOSITORY https://github.com/LunarWatcher/lua-cmake)
FetchContent_MakeAvailable(lua)
...
target_link_libraries(your-lib lua::lib)
```

To use a different version of lua, use `set(LUA_TAG "v1.2.3" CACHE STRING "" FORCE)` prior to the code above.
