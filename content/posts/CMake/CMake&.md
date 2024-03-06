+++
title = 'CMakeVcpkg(TODO)'
date = 2023-12-24T21:29:24+08:00
draft = false
series = ["CMake"]
series_weight = 1
toc = true
+++

[Microsoft 文档](https://learn.microsoft.com/zh-cn/vcpkg/users/buildsystems/cmake-integration)
## 在CMake项目中集成vcpkg
一般情况用以下方式一，如果触发了乱七八糟的问题试试方式二，比如方式一和.net core的项目冲突（3.29）
``` cmake
#方式一
set(CMAKE_TOOLCHAIN_FILE "**/vcpkg/scripts/buildsystems/vcpkg.cmake") 
#方式二
list(APPEND CMAKE_PREFIX_PATH "**/vcpkg/installed/x64-windows") 
```

## 使用CMake 创建.Net Core项目于
``` cmake

set(CMAKE_CSharp_FLAGS "/langversion:10.0 /platform:x64" )
#这一句一定放在C++项目后面，不然会给C++项目添加net8.0框架（当然是cmake的bug）
set(CMAKE_DOTNET_TARGET_FRAMEWORK "net8.0")
set(CMAKE_DOTNET_SDK "Microsoft.NET.Sdk")

enable_language(CSharp) #如果在子目录用这个，不然识别不了语言
project(MyNetProject LANGUAGES CSharp) #设置语言为CSharp

add_library(MyNetProject SHARED a.cs)

# 引用dll
set_property(TARGET MyNetProject PROPERTY VS_DOTNET_REFERENCES "***.dll") 

# 添加nuget包（名称_版本）
set_property(TARGET MyNetProject PROPERTY VS_PACKAGE_REFERENCES
"Newtonsoft.json_13.0.3" 
)
# 添加项目依赖
add_dependencies(MyNetProject WeldRecognition)
```

## 使用CPack 打包nuget
### 安装文件
打包前把需要的文件安装好，nuget规定了文件目录详情见[nuget](https://learn.microsoft.com/zh-cn/nuget/create-packages/creating-a-package)
- 根目录：放readme.txt
- **lib/{tfm}**：用于编译和运行时文件，包括`.dll`、`.xml` 和`.pdb`
- **runtimes**：仅用于运行时文件
- **build**： 放MSBuild `.targets` 和 `.props` 文件，会自动插入到项目中，如果希望将runtimes文件夹拷贝到运行目录则添加以下`.targets`文件
    ``` xml
    <?xml version="1.0"?>
    <Project>
        <ItemGroup>
            <Content Include="$(MSBuildThisFileDirectory)..\runtimes\**">
                <Visible>false</Visible>
                <PublishState>Included</PublishState>
                <CopyToOutputDirectory>Always</CopyToOutputDirectory>
                <Link>%(FileName)%(Extension)</Link>
                <Pack>false</Pack>
            </Content>
        </ItemGroup>
    </Project>
    ```
### 打包文件
``` cmake
set(CPACK_GENERATOR "NuGet")bao
set(CPACK_NUGET_PACKAGE_NAME "MyNetProject")
set(CPACK_NUGET_PACKAGE_VERSION "0.0.1")
set(CPACK_NUGET_PACKAGE_AUTHORS "XXX")
set(CPACK_NUGET_PACKAGE_DESCRIPTION "C# Wrapper")
set(CPACK_NUGET_PACKAGE_TAGS "焊接 焊缝提取")
set(CPACK_NUGET_PACKAGE_RELEASE_NOTES "-Init")
set(CPACK_PACKAGE_DIRECTORY ${CMAKE_SOURCE_DIR}/package)
include(CPack)
```

更多nuget相关属性见[CPack NuGet Generator](https://cmake.org/cmake/help/latest/cpack_gen/nuget.html#cpack_gen:CPack%20NuGet%20Generator)

最后以下命令执行，编译及打包
`cmake --build . --config Release`
`cpack -C Release`