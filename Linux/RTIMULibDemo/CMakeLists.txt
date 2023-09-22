#////////////////////////////////////////////////////////////////////////////
#//
#//  This file is part of RTIMULib
#//
#//  Copyright (c) 2014, richards-tech
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

SET(DEMO_SRCS
    AccelCalDlg.cpp
    RTIMULibDemo.cpp
    IMUThread.cpp
    MagCalDlg.cpp
    SelectIMUDlg.cpp
    SelectFusionDlg.cpp
    main.cpp)

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


INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_BINARY_DIR})

SET(CMAKE_AUTOMOC ON)

IF(DEFINED QT5)
    FIND_PACKAGE(Qt5Widgets)
    FIND_PACKAGE(Qt5Gui)
    qt5_wrap_ui(UI_HEADERS RTIMULibDemo.ui)
    ADD_EXECUTABLE(RTIMULibDemo ${DEMO_SRCS} ${UI_HEADERS})
    TARGET_LINK_LIBRARIES(RTIMULibDemo RTIMULib)
    qt5_use_modules(RTIMULibDemo Widgets Gui)
    INSTALL(TARGETS RTIMULibDemo DESTINATION bin)
ELSE(DEFINED QT5)
    FIND_PACKAGE(Qt4)
    IF(QT4_FOUND)
        QT4_WRAP_UI(UI_HEADERS RTIMULibDemo.ui)
        INCLUDE(${QT_USE_FILE})
        ADD_DEFINITIONS(${QT_DEFINITIONS})
        ADD_EXECUTABLE(RTIMULibDemo ${DEMO_SRCS} ${UI_HEADERS})
        TARGET_LINK_LIBRARIES(RTIMULibDemo RTIMULib ${QT_LIBRARIES})
        INSTALL(TARGETS RTIMULibDemo DESTINATION bin)
    ELSE(QT4_FOUND)
        MESSAGE(STATUS "Qt4 not found")
    ENDIF(QT4_FOUND)
ENDIF(DEFINED QT5)

