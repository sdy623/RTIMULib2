#////////////////////////////////////////////////////////////////////////////
#//
#//  This file is part of RTIMULib
#//
#//  Copyright (c) 2014-2015, richards-tech
#//
#//  Permission is hereby granted, free of charge, to any person obtaining a copy of
#//  this software and associated documentation files (the "Software"), to deal in
#//  the Software without restriction, including without limitation the rights to use,
#//  copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the
#//  Software, and to permit persons to whom the Software is furnished to do so,
#//  subject to the following conditions:
#//
#//  The above copyright notice and this permission notice shall be included in all
#//  copies or substantial portions of the Software.
#//
#//  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED,
#//  INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A
#//  PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
#//  HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
#//  OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
#//  SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

#// The cmake support was based on work by Moritz Fischer at ettus.com.
#// Original copyright notice:
#
# Copyright 2014 Ettus Research LLC
#

########################################################################
IF(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_BINARY_DIR})
    MESSAGE(FATAL_ERROR "Prevented in-tree built. This is bad practice.")
ENDIF(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_BINARY_DIR})

########################################################################
# Project setup
########################################################################
CMAKE_MINIMUM_REQUIRED(VERSION 2.8.9)
PROJECT(RTIMULib CXX)

IF(EMBEDDED_BUILD)
    # 基于EMBEDDED_BUILD设置进行静态编译和其他配置
    SET(STATIC_BUILD ON)
    SET(MINIMAL_BUILD ON)
    SET(BUILD_GL OFF)
    SET(BUILD_DEMOGL OFF)
    SET(BUILD_DEMO OFF)

    # 添加交叉编译设置
    SET(CMAKE_SYSTEM_NAME Linux)
    SET(CMAKE_SYSTEM_PROCESSOR aarch64)

    # 指定交叉编译器
    SET(CMAKE_C_COMPILER aarch64-linux-gnu-gcc)
    SET(CMAKE_CXX_COMPILER aarch64-linux-gnu-g++)

    # 指定目标环境
    SET(CMAKE_FIND_ROOT_PATH  /usr/aarch64-linux-gnu)

    # 在构建主机目录中搜索程序
    SET(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
    # 在目标目录中搜索库和头文件
    SET(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
    SET(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
    message("EMBEDDED_BUILD is set")
ENDIF(EMBEDDED_BUILD)

ENABLE_TESTING()

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})

INCLUDE(${CMAKE_CURRENT_SOURCE_DIR}/../RTIMULibVersion.txt)

SET(LIBRTIMU_SRCS
    RTFusion.cpp
    RTFusionRTQF.cpp
    RTMath.cpp
    RTFusionKalman4.cpp
    RTIMUAccelCal.cpp
    RTIMUHal.cpp
    RTIMUMagCal.cpp
    RTIMUSettings.cpp
    IMUDrivers/RTIMU.cpp
    IMUDrivers/RTIMUGD20M303DLHC.cpp
    IMUDrivers/RTIMUGD20HM303DLHC.cpp
    IMUDrivers/RTIMUGD20HM303D.cpp
    IMUDrivers/RTIMULSM9DS0.cpp
    IMUDrivers/RTIMULSM9DS1.cpp
    IMUDrivers/RTIMUMPU9150.cpp
    IMUDrivers/RTIMUMPU9250.cpp
    IMUDrivers/RTIMUBMX055.cpp
    IMUDrivers/RTIMUBNO055.cpp
    IMUDrivers/RTIMUNull.cpp
    IMUDrivers/RTPressure.cpp
    IMUDrivers/RTPressureBMP180.cpp
    IMUDrivers/RTPressureLPS25H.cpp
    IMUDrivers/RTPressureMS5611.cpp
    IMUDrivers/RTPressureMS5637.cpp
    IMUDrivers/RTHumidity.cpp
    IMUDrivers/RTHumidityHTS221.cpp
    IMUDrivers/RTHumidityHTU21D.cpp
    IMUDrivers/RTIMUHMC5883LADXL345.cpp
)

IF(WIN32 AND QT5)
    FIND_PACKAGE(Qt5Widgets)
    FIND_PACKAGE(Qt5Gui)
    qt5_wrap_ui(UI_HEADERS RTIMULibDemo.ui)
    IF(STATIC_BUILD)
        ADD_LIBRARY(RTIMULib STATIC ${LIBRTIMU_SRCS})
    else()
        ADD_LIBRARY(RTIMULib SHARED ${LIBRTIMU_SRCS})
    endif()
    qt5_use_modules(RTIMULib Widgets Gui)
ENDIF(WIN32 AND QT5)

IF(WIN32 AND (NOT QT5))
    FIND_PACKAGE(Qt4 REQUIRED)
    INCLUDE(${QT_USE_FILE})
    ADD_DEFINITIONS(${QT_DEFINITIONS})
    IF(STATIC_BUILD)
        ADD_LIBRARY(RTIMULib STATIC ${LIBRTIMU_SRCS})
    else()
        ADD_LIBRARY(RTIMULib SHARED ${LIBRTIMU_SRCS})
    endif()
    TARGET_LINK_LIBRARIES(RTIMULib ${QT_LIBRARIES})
ENDIF(WIN32 AND (NOT QT5))

IF(UNIX)
    IF(STATIC_BUILD)
        ADD_LIBRARY(RTIMULib STATIC ${LIBRTIMU_SRCS})
    else()
        ADD_LIBRARY(RTIMULib SHARED ${LIBRTIMU_SRCS})
    endif()
    SET_PROPERTY(TARGET RTIMULib PROPERTY VERSION ${RTIMULIB_VERSION})
    SET_PROPERTY(TARGET RTIMULib PROPERTY SOVERSION ${RTIMULIB_VERSION_MAJOR})
    INSTALL(TARGETS RTIMULib DESTINATION lib)
    INSTALL(DIRECTORY . DESTINATION include FILES_MATCHING PATTERN "*.h")
ENDIF(UNIX)

