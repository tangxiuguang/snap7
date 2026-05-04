cmake_minimum_required(VERSION 3.18)
project(snap7 LANGUAGES CXX C)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# ==========================
# 核心：必须加这个！Windows DLL 导出符号
# 不加这个，只会生成 dll，不会生成 lib！
# ==========================
if(WIN32)
  set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
endif()

# ==========================
# 自动收集 Snap7 源码
# ==========================
file(GLOB_RECURSE SNAP7_SOURCES
    #${CMAKE_CURRENT_SOURCE_DIR}/src/snap7.cpp
    #${CMAKE_CURRENT_SOURCE_DIR}/src/sys/*.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/sys/*.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/src/core/*.cpp
    #${CMAKE_CURRENT_SOURCE_DIR}/src/lib/*.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/lib/*.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/src/lib/*.def
)

# ==========================
# 头文件路径
# ==========================
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/src/sys)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/src/core)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/src/lib)

# ==========================
# 生成动态库（SHARED）
# ==========================
add_library(${PROJECT_NAME} SHARED ${SNAP7_SOURCES})

# ==========================
# Windows 依赖库
# ==========================
if(WIN32)
    target_compile_definitions(${PROJECT_NAME} PRIVATE
        _CRT_SECURE_NO_WARNINGS
        WIN32
        _WINSOCKAPI_
		OS_WINDOWS
		SNAP7_CLIENT
		SNAP7_NO_SERVER
    )
    target_link_libraries(${PROJECT_NAME} PRIVATE ws2_32 winmm)
endif()

# ==========================
# Linux 依赖
# ==========================
if(UNIX AND NOT APPLE)
    target_compile_definitions(${PROJECT_NAME} PRIVATE UNIX)
    target_link_libraries(${PROJECT_NAME} PRIVATE pthread rt)
endif()