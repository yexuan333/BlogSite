+++
title = 'CMake常见变量'
date = 2023-12-24T21:11:43+08:00
draft = false
series = ["CMake"]
series_weight = 4
toc = true
+++

[官方文档](https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html)
[中文文档](https://cmake-doc.readthedocs.io/zh-cn/latest/manual/cmake-env-variables.7.html)

### 项目和版本信息
- `CMAKE_PROJECT_NAME`: 项目名称。
- `PROJECT_NAME`: 当前处理的项目名称。
- `PROJECT_VERSION`, `PROJECT_VERSION_MAJOR`, `PROJECT_VERSION_MINOR`, `PROJECT_VERSION_PATCH`, `PROJECT_VERSION_TWEAK`: 项目的版本信息。
### 路径和目录
- `CMAKE_SOURCE_DIR`: 顶级源代码目录的路径。
- `CMAKE_CURRENT_SOURCE_DIR`: 当前处理的源代码目录的路径。
- `CMAKE_BINARY_DIR`: 顶级构建目录的路径。
- `CMAKE_CURRENT_BINARY_DIR`: 当前处理的构建目录的路径。
- `CMAKE_INSTALL_PREFIX`: 安装路径前缀（默认是 /usr/local 或 C:/Program Files 等）。
### 编译器和工具设置
- `CMAKE_C_COMPILER`: C编译器的路径。
- `CMAKE_CXX_COMPILER`: C++编译器的路径。
- `CMAKE_BUILD_TYPE`: 构建类型（如 Release 或 Debug）。
 -`CMAKE_C_FLAGS`, `CMAKE_CXX_FLAGS`: C/C++编译器的特定标志。
### 平台和系统信息
- `CMAKE_SYSTEM_NAME`: 系统名称，例如 Linux、Windows。
- `CMAKE_SYSTEM_PROCESSOR`: 处理器名称，例如 x86_64、AMD64。
- `WIN32`, `UNIX`, `APPLE`: 特定平台的布尔变量。
### 安装和目录选项
- `CMAKE_INSTALL_BINDIR`, `CMAKE_INSTALL_LIBDIR`, `CMAKE_INSTALL_INCLUDEDIR`: 用于安装二进制文件、库和头文件的目录。
- `CMAKE_MODULE_PATH`: CMake模块查找路径。
### 功能和条件判断
- `BUILD_SHARED_LIBS`: 是否构建共享库，而不是静态库。