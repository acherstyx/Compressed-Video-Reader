cmake_minimum_required(VERSION 3.4...3.18)
project(cv_reader)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -pthread -ldl -lutil")

SET(CMAKE_EXE_LINKER_FLAGS
        "${CMAKE_EXE_LINKER_FLAGS} -Wl,-rpath -Wl,${PROJECT_SOURCE_DIR}/ffmpeg/ffmpeg_install/lib")
set(ENV{PKG_CONFIG_PATH} "${PROJECT_SOURCE_DIR}/ffmpeg/ffmpeg_install/lib/pkgconfig")
set(LIBAV_INCLUDE_DIRS "${PROJECT_SOURCE_DIR}/ffmpeg/ffmpeg_install/include")
set(Python3_EXECUTABLE /usr/bin/python3)

find_package(Python3 REQUIRED COMPONENTS Development NumPy REQUIRED)
find_package(PkgConfig REQUIRED)
find_package(PythonLibs REQUIRED)

pkg_check_modules(AVCODEC REQUIRED IMPORTED_TARGET libavcodec)
pkg_check_modules(AVFORMAT REQUIRED IMPORTED_TARGET libavformat)
pkg_check_modules(AVFILTER REQUIRED IMPORTED_TARGET libavfilter)
pkg_check_modules(AVDEVICE REQUIRED IMPORTED_TARGET libavdevice)
pkg_check_modules(AVUTIL REQUIRED IMPORTED_TARGET libavutil)
pkg_check_modules(SWRESAMPLE REQUIRED IMPORTED_TARGET libswresample)
pkg_check_modules(SWSCALE REQUIRED IMPORTED_TARGET libswscale)

message(STATUS "CMAKE_EXE_LINKER_FLAGS: ${CMAKE_EXE_LINKER_FLAGS}")
message(STATUS "LIBAV_INCLUDE_DIRS: ${LIBAV_INCLUDE_DIRS}")
message(STATUS "Python3_INCLUDE_DIRS: ${Python3_INCLUDE_DIRS}")
message(STATUS "Python3_LIBRARY_DIRS: ${Python3_LIBRARY_DIRS}")
message(STATUS "Python3_NumPy_INCLUDE_DIRS: ${Python3_NumPy_INCLUDE_DIRS}")

# compressed video reader python module
add_library(cv_reader SHARED src/cv_reader/api.cpp)
set_target_properties(
        cv_reader
        PROPERTIES
        PREFIX ""
        OUTPUT_NAME "cv_reader"
        LINKER_LANGUAGE C
)
target_include_directories(
        cv_reader PUBLIC
        src/cv_reader
        ${LIBAV_INCLUDE_DIRS}
        ${FFMPEG_SRC_ROOT}
        ${Python3_INCLUDE_DIRS}
)
target_link_libraries(
        cv_reader PUBLIC
        PkgConfig::AVCODEC
        PkgConfig::AVFORMAT
        PkgConfig::AVFILTER
        PkgConfig::AVDEVICE
        PkgConfig::AVUTIL
        PkgConfig::SWRESAMPLE
        PkgConfig::SWSCALE
        ${Python3_LIBRARIES}
        Python3::NumPy
)
