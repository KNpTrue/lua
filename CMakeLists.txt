cmake_minimum_required(VERSION 3.5)

project(lua
    VERSION 5.4.4
    LANGUAGES C
)

add_library(lua_static STATIC
    src/lapi.c
    src/lcode.c
    src/lctype.c
    src/ldebug.c
    src/ldo.c
    src/ldump.c
    src/lfunc.c
    src/lgc.c
    src/llex.c
    src/lmem.c
    src/lobject.c
    src/lopcodes.c
    src/lparser.c
    src/lstate.c
    src/lstring.c
    src/ltable.c
    src/ltm.c
    src/lundump.c
    src/lvm.c
    src/lzio.c
    src/lauxlib.c
    src/lbaselib.c
    src/lcorolib.c
    src/ldblib.c
    src/liolib.c
    src/lmathlib.c
    src/loadlib.c
    src/loslib.c
    src/lstrlib.c
    src/ltablib.c
    src/lutf8lib.c
    src/linit.c
)
set_target_properties(lua_static PROPERTIES
    OUTPUT_NAME "lua"
)

add_executable(lua
    src/lua.c
)
target_link_libraries(lua PRIVATE lua_static)

add_executable(luac
    src/luac.c
)
target_link_libraries(luac PRIVATE lua_static)

target_compile_definitions(lua_static PUBLIC LUA_COMPAT_5_3)
target_link_libraries(lua_static PUBLIC m)

option(LUA_USE_C89 "Specifies the C standard to C89." OFF)
if (LUA_USE_C89)
    message(WARNING
        "C89 does not guarantee 64-bit integers for Lua. " 
        "Make sure to compile all external Lua libraries "
        "with LUA_USE_C89 to ensure consistency."
    )
    set(CMAKE_C_STANDARD 89)
else ()
    set(CMAKE_C_STANDARD 99)
endif ()

if (${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
    option(LUA_USE_READLINE "Support readline." OFF)
    if (LUA_USE_READLINE)
        target_compile_definitions(lua PRIVATE LUA_USE_READLINE)
        target_link_libraries(lua PRIVATE readline)
    endif ()

    target_compile_definitions(lua_static PUBLIC LUA_USE_LINUX)
    target_link_libraries(lua_static PRIVATE dl)
endif ()
