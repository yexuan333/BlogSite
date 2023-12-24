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

## 1. 变量与缓存

#### 1.1 本地变量
声明一个本地 ( local ) 变量：

`set(MY_VARIABLE "value")`

变量名通常全部用大写，变量值跟在其后。你可以通过 `${}` 来解析一个变量，例如 `${MY_VARIABLE}`, CMake 有作用域的概念，在声明一个变量后，你只可以在它的作用域内访问这个变量。如果你将一个函数或一个文件放到一个子目录中，这个变量将不再被定义。你可以通过在变量声明末尾添加 `PARENT_SCOPE` 来将它的作用域置定为当前的上一级作用域。

声明一个列表类型的变量,`;` 分隔变量和空格的作用是一样的：

`set(MY_LIST "one" "two")`

`set(MY_LIST "one;two")`

#### 1.2 缓存变量

CMake 提供了一个缓存变量来允许你从命令行或cmake-gui中设置变量, 这么写不会覆盖已定义的值

`set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "Description")`

#### 1.3 环境变量

`set(ENV{variable_name} value)` 和 `$ENV{variable_name}` 来设置和获取环境变量

## 2. 缓存
缓存实际上就是个文本文件，CMakeCache.txt ，当你运行 CMake 构建目录时会创建它。 CMake 可以通过它来记住你设置的所有东西，因此你可以不必在重新运行 CMake 的时候再次列出所有的选项。

## 3. 简单的例子
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