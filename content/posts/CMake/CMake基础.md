+++
title = 'CMake基础'
date = 2023-12-23T16:00:42+08:00
draft = false
series = ["CMake"]
series_weight = 2
toc = true
+++

[cmkae下载](https://cmake.org/download/)

[cmake 官方教程](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)

## Introduction to CMake
CMake 是一个开源的、跨平台的自动化构建系统，用于管理软件构建过程。它使用名为 CMakeLists.txt 的文件来描述构建过程，这使得它与传统的 Makefile 或项目文件不同。

### What is CMake
CMake 不是一个构建工具，而是一个构建工具生成器。它可以为多种平台和工具生成标准的构建文件，如 Makefiles、Visual Studio 项目文件等。这意味着，开发者只需编写一次 CMake 脚本，就可以在多个平台上构建他们的项目。

CMake 的主要功能 (Main features of CMake)
- 跨平台：CMake 支持多种操作系统和编译器，如 Linux、Windows、macOS、GCC、Clang、Visual Studio 等。
- 灵活性：CMake 允许开发者为不同的平台和编译器指定不同的构建选项和设置。
- 模块化：CMake 有一个强大的模块系统，可以轻松地找到、使用和链接各种库。
- 可扩展性：开发者可以为 CMake 编写自己的模块和脚本，以满足特定的构建需求。

要使用 CMake 生成构建系统，必须选择以下内容：

- **源代码目录**：包含项目提供的源文件的顶级目录。从名为 CMakeLists.txt 的顶级文件开始。这些文件指定构建目标及其依赖项
- **构建目录**：要存储构建系统文件和构建输出工件（例如可执行文件和库）的顶级目录。 CMake 将编写一个 CMakeCache.txt 文件来将该目录标识为构建树并存储持久性信息，例如构建系统配置选项。
- **生成器**：这选择了要生成的构建系统的种类。运行 cmake --help 查看本地可用的生成器列表。可以选择使用下面的 -G 选项来指定生成器。

### Run CMake
你始终应该建立一个专用于构建的目录并在那里构建项目。从技术上来讲，你可以进行内部构建（即在源代码目录下执行 CMake 构建命令），但是必须注意不要覆盖文件或者把它们添加到 git。

```bash
~/package $ mkdir build
~/package $ cd build
~/package/build $ cmake ..
~/package/build $ make
```

## 1. Example
```cmake 
cmake_minimum_required(VERSION 3.1...3.21)

# This is your project statement. You should always list languages;
# Listing the version is nice here since it sets lots of useful variables
project(
  CMakeExample
  VERSION 1.0
  LANGUAGES CXX)

# Find packages go here.

# You should usually split this into folders, but this is a simple example

# This is a "default" library
add_library(MyLibExample simple_lib.cpp simple_lib.hpp)

# Link each target with other targets or add options, etc.

# Adding something we can run - Output name matches target name
add_executable(MyExample simple_example.cpp)

target_link_libraries(MyExample PRIVATE MyLibExample)
```

`set(CMAKE_CXX_STANDARD 11)`  启用对特定 C++ 标准的支持

`set(CMAKE_CXX_STANDARD_REQUIRED True)` 确保标准的可用性，如果指定的标准不可用，CMake配置过程将失败。

`project(HelloWorld VERSION 1.2.3)` 设置项目版本号

在设置了版本号之后，CMake会提供一些变量来代表这些值：
- `PROJECT_VERSION` - 完整的版本号，例如 "1.2.3"。
- `PROJECT_VERSION_MAJOR` - 主版本号，例如 "1"。
- `PROJECT_VERSION_MINOR` - 副版本号，例如 "2"。
- `PROJECT_VERSION_PATCH` - 补丁版本号，例如 "3"。
  
`target_precompile_headers`添加要预编译的头文件列表。

`target_sources`将源添加到目标。

#### 3.1 创建库或可执行文件
**add_library**
  - `add_library(<name> [STATIC | SHARED | MODULE] [EXCLUDE_FROM_ALL] [<source>...])` 使用指定的源文件将库添加到项目中。
  - `STATIC` 库是目标文件的存档，供链接其他目标时使用，生成.lib（Windows）.a（Unix）
  - `SHARED` 库动态链接并在运行时加载，生成.dll（Windows）.so（Unix）
  - `MODULE` 库是不链接到其他目标但可以在运行时通过程序的某种形式动态加载的库
  - `add_library(<name> ALIAS <target>)`别名库

**add_executable**
  - `add_executable(<name> [EXCLUDE_FROM_ALL] [<source>...])` 使用指定的源文件向项目添加可执行文件。
  - 在 3.11 版本加入: 如果稍后使用 `target_sources()` 添加源文件，则可以省略加入源文件。
  - `add_executable(<name> IMPORTED [GLOBAL])`导入可执行文件，后续可在`add_custom_command()` 等命令中引用
  - `add_executable(<name> ALIAS <target>)`别名可执行文件


#### 3.2 添加库
**target_XXX和XXX的区别**
- `target_XXX` 指定给定目标，且指定的 `target` 必须由命令创建，例如 `add_executable()` 或 `add_library()` 并且不能是 `ALIAS target`。
- `XXX` 会影响该文件及其子目录中定义的所有目标（包括可执行文件和库）（不会向上传播）
  
**传递选项**
- `PRIVATE`，仅被定义目标需要，并且不会传递给依赖该目标的其他目标。
- `PUBLIC`，链接库对定义目标和依赖该目标的其他目标都是可见的。
- `INTERFACE`，用于当你不需要将库链接到当前目标，但希望将链接要求传递给依赖该目标的其他目标。这仅适用于库的接口（如头文件路径、编译定义等）

**链接库**
- `include_directories``target_include_directories` 将包含目录添加到目标。

- `link_libraries``target_link_libraries`指定链接给定目标和/或其依赖项时要使用的库或标志。

- `link_directories``target_link_directories`将链接目录添加到目标。

- `add_compile_definitions``target_compile_definitions` 将编译定义添加到目标。
  - 项目上任何前导的 -D 都将被删除。空项将被忽略。例如，以下都是等价的：
    ```cmake
    target_compile_definitions(foo PUBLIC FOO)
    target_compile_definitions(foo PUBLIC -DFOO)  # -D removed
    target_compile_definitions(foo PUBLIC "" FOO) # "" ignored
    target_compile_definitions(foo PUBLIC -D FOO) # -D becomes "", then ignored
    ```

### Do's and Don'ts 
[Effective Modern CMake](https://gist.github.com/mbinna/c61dbb39bca0e4fb7d1f73b0d66a4fd1)

### [Release Notes](https://cmake-doc.readthedocs.io/zh-cn/latest/release/index.html)