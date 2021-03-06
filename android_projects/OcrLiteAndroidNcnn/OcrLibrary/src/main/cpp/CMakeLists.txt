project(OcrLite)

cmake_minimum_required(VERSION 3.4.1)

option(OCR_LITE_OPENMP "OcrLite Enable OpenMP" ON)
option(OCR_LITE_VULKAN "OcrLite Enable Vulkan" ON)
set(OCR_LITE_OPENMP ON)
set(OCR_LITE_VULKAN OFF)
set(ncnn_DIR "${CMAKE_SOURCE_DIR}/../ncnn-static/${ANDROID_ABI}/lib/cmake/ncnn")
find_package(ncnn REQUIRED)

## opencv 库
set(OpenCV_DIR "${CMAKE_SOURCE_DIR}/../../sdk/native/jni")
find_package(OpenCV REQUIRED)
if (OpenCV_FOUND)
    message(STATUS "OpenCV_LIBS: ${OpenCV_LIBS}")
    message(STATUS "OpenCV_INCLUDE_DIRS: ${OpenCV_INCLUDE_DIRS}")
else ()
    message(FATAL_ERROR "opencv Not Found!")
endif (OpenCV_FOUND)

include_directories(include)
file(GLOB OCR_SRC src/*.cpp)
set(OCR_COMPILE_CODE ${OCR_SRC})

add_library(OcrLite SHARED ${OCR_COMPILE_CODE})

if (OCR_LITE_OPENMP)
    target_compile_definitions(OcrLite PRIVATE __OPENMP__)
endif ()

target_link_libraries(
        OcrLite
        ncnn
        ${OpenCV_LIBS}
        jnigraphics)

if (OCR_LITE_VULKAN)
    target_compile_definitions(OcrLite PRIVATE __VULKAN__)
endif ()