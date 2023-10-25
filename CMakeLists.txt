cmake_minimum_required(VERSION 3.15)
set(name "capirinha")
set(NDK_VER "r26b")
set(ANDROID_ABI "arm64-v8a")

set(ANDROID_NATIVE_API_LEVEL 29) # TLS fix
set(CMAKE_TOOLCHAIN_FILE "${CMAKE_SOURCE_DIR}/android-ndk-${NDK_VER}/build/cmake/android.toolchain.cmake")
set(CMAKE_FIND_LIBRARY_SUFFIXES ".a")
set(BUILD_SHARED_LIBS OFF)
set(CMAKE_EXE_LINKER_FLAGS "-static -ffunction-sections -fdata-sections -Wl,--gc-sections")
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
project(${name})

add_executable(capirinha
    ${CMAKE_SOURCE_DIR}/src/main.cpp
)

target_link_libraries(${name} -static)

add_custom_command(TARGET ${name} POST_BUILD
                   COMMAND ${CMAKE_COMMAND} -E copy
                   ${CMAKE_SOURCE_DIR}/capirinha
                   ${CMAKE_SOURCE_DIR}/res/META-INF/com/google/android/update-binary
)

add_custom_command(TARGET ${name} POST_BUILD
                   COMMAND ${CMAKE_COMMAND} -E tar cvf "../update.zip" --format=zip "assets" "META-INF"
                   WORKING_DIRECTORY "${CMAKE_SOURCE_DIR}/res"
)