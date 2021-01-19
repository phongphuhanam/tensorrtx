cmake_minimum_required(VERSION 2.6)

project(yolov5)

add_definitions(-std=c++11)

option(CUDA_USE_STATIC_CUDA_RUNTIME OFF)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_BUILD_TYPE Debug)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/../cmake)
find_package(CUDA REQUIRED)
# message("-- Found CUDA: " ${CUDA_FOUND})
# message("-- CUDA library " ${CUDA_INCLUDE_DIRS})
find_package(CUDNN REQUIRED)

include_directories(${PROJECT_SOURCE_DIR}/include ${CUDA_INCLUDE_DIRS} ${CUDNN_INCLUDE_DIR} ${NVINFER_INCLUDE_DIR})

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Ofast -Wfatal-errors -D_MWAITXINTRIN_H_INCLUDED")

# IF (NOT DEFINED _MYDEFINE)
#     SET(_MYDEFINE <default value>)
# ENDIF()
# ADD_DEFINITIONS(-D_MYDEFINE=${_MYDEFINE})

cuda_add_library(myplugins SHARED ${PROJECT_SOURCE_DIR}/yololayer.cu)
target_link_libraries(myplugins ${CUDNN_LIBRARIES} ${CUDART_LIBRARY})

find_package(OpenCV)
include_directories(OpenCV_INCLUDE_DIRS)

add_executable(yolov5 ${PROJECT_SOURCE_DIR}/yolov5.cpp)
# target_link_libraries(yolov5 nvinfer)
# target_link_libraries(yolov5 cudart)
target_link_libraries(yolov5 myplugins ${OpenCV_LIBS})
# target_link_libraries(yolov5 ${OpenCV_LIBS})

add_executable(yolov5_map ${PROJECT_SOURCE_DIR}/yolov5_map.cpp)
# target_link_libraries(yolov5_map nvinfer)
# target_link_libraries(yolov5_map cudart)
target_link_libraries(yolov5_map myplugins ${OpenCV_LIBS})
# target_link_libraries(yolov5_map )


add_definitions(-O2 -pthread)

