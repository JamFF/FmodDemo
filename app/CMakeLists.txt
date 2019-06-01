# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Sets the minimum version of CMake required to build the native library.

cmake_minimum_required(VERSION 3.4.1)

# 设置生成的so动态库最后输出的目录，CMAKE_SOURCE_DIR就是CMakeLists.txt所在的路径，在gradle中配置
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/../jniLibs/${CMAKE_ANDROID_ARCH_ABI})

# CMAKE_SOURCE_DIR 和 PROJECT_SOURCE_DIR 这两个变量在AS目录结构下是一样的，在CMake的其它应用场景，可能会不一样
message("CMAKE_SOURCE_DIR = ${CMAKE_SOURCE_DIR}")
message("PROJECT_SOURCE_DIR = ${PROJECT_SOURCE_DIR}")

# 设置头文件目录，C文件中include时不需要带路径了
include_directories(${CMAKE_SOURCE_DIR}/src/main/cpp/inc)

# Creates and names a library, sets it as either STATIC
# or SHARED, and provides the relative paths to its source code.
# You can define multiple libraries, and CMake builds them for you.
# Gradle automatically packages shared libraries with your APK.

add_library( # Sets the name of the library.
        play_sound

        # Sets the library as a shared library.
        SHARED

        # Provides a relative path to your source file(s).
        src/main/cpp/common_platform.cpp
        src/main/cpp/play_sound.cpp
        src/main/cpp/common.cpp)

set(ABI_DIR ${CMAKE_SOURCE_DIR}/src/main/jniLibs/${CMAKE_ANDROID_ARCH_ABI})

#[[add_library(fmod SHARED IMPORTED)
# 设置导入的路径
set_target_properties(fmod
        PROPERTIES IMPORTED_LOCATION
        ${ABI_DIR}/libfmod.so)

add_library(fmodL SHARED IMPORTED)
# 设置导入的路径
set_target_properties(fmodL
        PROPERTIES IMPORTED_LOCATION
        ${ABI_DIR}/libfmodL.so)]]

# 设置第三方so库的路径，可以一起引入多个库，可代替上面libfmod.so和libfmodL.so单独引用的多行代码
# Android 6.0以后推荐使用，上面的可能会有bug，暂时没发现。
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -L${ABI_DIR}")

# Searches for a specified prebuilt library and stores the path as a
# variable. Because CMake includes system libraries in the search path by
# default, you only need to specify the name of the public NDK library
# you want to add. CMake verifies that the library exists before
# completing its build.

find_library( # Sets the name of the path variable.
        log-lib

        # Specifies the name of the NDK library that
        # you want CMake to locate.
        log)

# Specifies libraries CMake should link to your target library. You
# can link multiple libraries, such as libraries you define in this
# build script, prebuilt third-party libraries, or system libraries.

target_link_libraries( # Specifies the target library.
        play_sound
        fmod
        fmodL

        # Links the target library to the log library
        # included in the NDK.
        ${log-lib})