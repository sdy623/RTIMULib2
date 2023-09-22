cmake_minimum_required(VERSION 3.3 FATAL_ERROR)

project(eillipose_fit)

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

find_package (Eigen3 3.3 REQUIRED NO_MOUDLE)

include_directories(${EIGEN3_INCLUDE_DIR})

IF(STATIC_BUILD)
    ADD_LIBRARY(RTEllipsoidFit STATIC ellipsoid_fit.cpp)
    ADD_DEFINITIONS(-DSTATIC_BUILD=1)
else()
    add_library(RTEllipsoidFit SHARED ellipsoid_fit.cpp)
    add_executable (ellipsoid_fit_standalone ellipsoid_fit.cpp)
    target_link_libraries(ellipsoid_fit_standalone Eigen3::Eigen)
endif()

target_link_libraries(RTEllipsoidFit Eigen3::Eigen)
set_target_properties(RTEllipsoidFit PROPERTIES PUBLIC_HEADER ellipsoid_fit.h)
install(TARGETS RTEllipsoidFit
    LIBRARY
    PUBLIC_HEADER)
