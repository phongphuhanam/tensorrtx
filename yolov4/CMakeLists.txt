cmake_minimum_required(VERSION 2.6)

project(yolov4)

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

IF (NOT DEFINED NMS_THRESH)
    SET(NMS_THRESH 0.65)
ENDIF()
IF (NOT DEFINED BBOX_CONF_THRESH)
    SET(BBOX_CONF_THRESH 0.001)
ENDIF()

IF (NOT DEFINED BATCH_SIZE)
    SET(BATCH_SIZE 1)
ENDIF()

# IF (NOT DEFINED NET)
#     SET(NET s)
# ENDIF()

add_definitions(-DNMS_THRESH=${NMS_THRESH} -DCONF_THRESH=${CONF_THRESH} -DBATCH_SIZE=${BATCH_SIZE} -DNET=${NET})

if (DEFINED USE_FP16)
    add_definitions(-DUSE_FP16)
endif()


find_package(OpenCV)
include_directories(OpenCV_INCLUDE_DIRS)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Ofast -Wfatal-errors -D_MWAITXINTRIN_H_INCLUDED")

cuda_add_library(myplugins SHARED ${PROJECT_SOURCE_DIR}/yololayer.cu ${PROJECT_SOURCE_DIR}/mish.cu)
target_link_libraries(myplugins ${CUDNN_LIBRARIES} ${CUDART_LIBRARY})


add_executable(yolov4 ${PROJECT_SOURCE_DIR}/yolov4.cpp)
target_link_libraries(yolov4 myplugins ${OpenCV_LIBS})

add_executable(yolov4_map ${PROJECT_SOURCE_DIR}/yolov4_map.cpp)
target_link_libraries(yolov4_map myplugins ${OpenCV_LIBS})

add_definitions(-O2 -pthread)

