+++
title = 'CMake简介'
date = 2023-12-24T03:07:04+08:00
draft = false
series = ["CMake"]
series_weight = 1
toc = true
+++

[中文文档](https://cmake-doc.readthedocs.io/zh-cn/latest/index.html#)
[Modern CMake文档](https://modern-cmake-cn.github.io/Modern-CMake-zh_CN/)
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
以下任何一条命令都能够执行安装：
```bash
# From the build directory (pick one)
~/package/build $ make install
~/package/build $ cmake --build . --target install
~/package/build $ cmake --install . # CMake 3.15+ only

# From the source directory (pick one)
~/package $ make -C build install
~/package $ cmake --build build --target install
~/package $ cmake --install build # CMake 3.15+ only
```

### Do's and Don'ts 
参考[Effective Modern CMake](https://gist.github.com/mbinna/c61dbb39bca0e4fb7d1f73b0d66a4fd1)

### [发行说明](https://cmake-doc.readthedocs.io/zh-cn/latest/release/index.html)

