cmake_minimum_required(VERSION 3.20)

project(lgfx_simulator)

add_definitions(-DLGFX_SDL)

file(GLOB LGFX_SRC RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} CONFIGURE_DEPENDS
        dependencies/LovyanGFX/src/lgfx/Fonts/efont/*.c
        dependencies/LovyanGFX/src/lgfx/Fonts/IPA/*.c
        dependencies/LovyanGFX/src/lgfx/utility/*.c
        dependencies/LovyanGFX/src/lgfx/v1/*.cpp
        dependencies/LovyanGFX/src/lgfx/v1/misc/*.cpp
        dependencies/LovyanGFX/src/lgfx/v1/panel/Panel_Device.cpp
        dependencies/LovyanGFX/src/lgfx/v1/panel/Panel_FrameBufferBase.cpp
        dependencies/LovyanGFX/src/lgfx/v1/platforms/sdl/*.cpp
)

add_library(LGFX_LIB ${LGFX_SRC})
target_include_directories(LGFX_LIB PUBLIC "dependencies/LovyanGFX/src/")
target_compile_features(LGFX_LIB PUBLIC cxx_std_17)

IF (CMAKE_SYSTEM_NAME MATCHES "Linux")
    find_package(SDL2 REQUIRED SDL2)
    target_link_libraries(LGFX_LIB -lpthread ${SDL2_LIBRARIES})
ELSEIF (CMAKE_SYSTEM_NAME MATCHES "Windows")
    find_package(SDL2 CONFIG REQUIRED)
    target_link_libraries(LGFX_LIB
        #        $<TARGET_NAME_IF_EXISTS:SDL2::SDL2main> #windows链接这个库就会报错
        $<IF:$<TARGET_EXISTS:SDL2::SDL2>,SDL2::SDL2,SDL2::SDL2-static>
    )
ELSEIF (CMAKE_SYSTEM_NAME MATCHES "Darwin")
    find_package(SDL2 REQUIRED COMPONENTS SDL2)
    target_link_libraries(LGFX_LIB PRIVATE SDL2::SDL2)
ENDIF ()

option(LGFX_SIMULATOR_BUILD "simulator example" ON)

# Example
if(LGFX_SIMULATOR_BUILD)
    add_subdirectory(example/fps_show)
    add_subdirectory(example/random)
endif()

# # CTest
# enable_testing()
# add_test(basic example/basic)


