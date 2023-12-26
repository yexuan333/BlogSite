+++
title = 'CMake导出与安装(Doing)'
date = 2023-12-24T21:03:04+08:00
draft = false
series = ["CMake"]
series_weight = 3
toc = true
+++

## 3. 安装
[官方文档](https://cmake.org/cmake/help/latest/command/install.html#command:install)

`cmake --install .` 执行安装命令

`cmake --install . --config Release` 以Release模式安装

`cmake --install . --prefix "/home/myuser/installdir"` 指定安装根目录安装，也可以设置`CMAKE_INSTALL_PREFIX`变量实现

```cmake
include(GNUInstallDirs)含义
# 提供一种跨平台的方式来处理安装路径，确保项目按照GNU的标准安装目录结构来安装。它在不同系统间提供了一致性，并使得项目更易于在不同Linux发行版上打包和分发。

```
安装目标的相关文件：

```cmake
install(TARGETS HelloWorld
        CONFIGURATIONS Release
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        PRIVATE_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
        PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_LIBDIR}
        HEADERS DESTINATION ${CMAKE_INSTALL_LIBDIR})
```
安装文件，通过这种形式安装的文件默认具有权限设置，OWNER_WRITE（所有者写权限）、OWNER_READ（所有者读权限）、GROUP_READ（组读权限）和WORLD_READ（所有用户读权限），如果没有给出其他参数的话。

安装程序，与 FILES 形式相同，除了安装文件的默认权限还包括 OWNER_EXECUTE（所有者执行权限）、GROUP_EXECUTE（组执行权限）和 WORLD_EXECUTE（所有用户执行权限）。这种形式旨在安装非TARGETS目标程序，例如脚本文件。

必须提供`TYPE`或`DESTINATION`参数之一
- `TYPE`参数指定了被安装文件的通用文件类型。然后，安装目标位置会通过从`GNUInstallDirs`中获取相应的变量自动设置，或者如果该变量未定义，则使用内置默认值。
- `DESTINATION`可设置相对路径或绝对路径

```cmake
install(<FILES|PROGRAMS> <file>...
        TYPE <type> | DESTINATION <dir>
        [PERMISSIONS <permission>...]
        [CONFIGURATIONS <config>...]
        [COMPONENT <component>]
        [RENAME <name>] [OPTIONAL] [EXCLUDE_FROM_ALL])
```

安装目录

```cmake
install(DIRECTORY dirs...
        TYPE <type> | DESTINATION <dir>
        [FILE_PERMISSIONS <permission>...]
        [DIRECTORY_PERMISSIONS <permission>...]
        [USE_SOURCE_PERMISSIONS] [OPTIONAL] [MESSAGE_NEVER]
        [CONFIGURATIONS <config>...]
        [COMPONENT <component>] [EXCLUDE_FROM_ALL]
        [FILES_MATCHING]
        [[PATTERN <pattern> | REGEX <regex>]
         [EXCLUDE] [PERMISSIONS <permission>...]] [...])
```

安装脚本或代码，代码会被执行
```cmake
install([[SCRIPT <file>] [CODE <code>]]
        [ALL_COMPONENTS | COMPONENT <component>]
        [EXCLUDE_FROM_ALL] [...])
```
安装一个 CMake 文件，为依赖项目导出目标：
```cmake
install(EXPORT <export-name> DESTINATION <dir>
        [NAMESPACE <namespace>] [FILE <name>.cmake]
        [PERMISSIONS <permission>...]
        [CONFIGURATIONS <config>...]
        [CXX_MODULES_DIRECTORY <directory>]
        [EXPORT_LINK_INTERFACE_LIBRARIES]
        [COMPONENT <component>]
        [EXCLUDE_FROM_ALL])
install(EXPORT_ANDROID_MK <export-name> DESTINATION <dir> [...])
```
安装运行时依赖项集：
```cmake
install(RUNTIME_DEPENDENCY_SET <set-name>
        [[LIBRARY|RUNTIME|FRAMEWORK]
         [DESTINATION <dir>]
         [PERMISSIONS <permission>...]
         [CONFIGURATIONS <config>...]
         [COMPONENT <component>]
         [NAMELINK_COMPONENT <component>]
         [OPTIONAL] [EXCLUDE_FROM_ALL]
        ] [...]
        [PRE_INCLUDE_REGEXES <regex>...]
        [PRE_EXCLUDE_REGEXES <regex>...]
        [POST_INCLUDE_REGEXES <regex>...]
        [POST_EXCLUDE_REGEXES <regex>...]
        [POST_INCLUDE_FILES <file>...]
        [POST_EXCLUDE_FILES <file>...]
        [DIRECTORIES <dir>...]
        )
```

## 4. 测试

## 5. 打包