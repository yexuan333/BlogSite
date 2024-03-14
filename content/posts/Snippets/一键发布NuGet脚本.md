+++
title = '一键发布NuGet脚本'
date = 2024-3-14T21:11:43+08:00
draft = false
series = ["Snippets"]
toc = true
+++

注意事项
- 脚本在.sln路径执行，输入对应项目名称，确保 .props 和 .csproj 文件路径正确
通过这个脚本，你可以实现项目版本的自动更新、构建、打包和发布流程，大大提高开发效率并减少人为错误。

1. **选择项目**:脚本首先提示用户输入项目名称
2. **输入发布说明**:用户被提示输入发布说明（Release Note），该说明将被用于更新 .props 文件中的 PackageReleaseNotes 属性。
3. **获取最新版本号**:脚本构造了一个 URL 来访问项目的 NuGet 注册信息，并发送 HTTP GET 请求以获取项目的最新版本号。然后，脚本将版本号的修订部分（最后一个数字）加一，以准备新版本的发布。
4. **更新 .props 文件**:脚本根据前面的输入和版本号计算，更新 .props 文件中的 PackageVersion、Version 和 PackageReleaseNotes 属性。这些更改随后被保存回 .props 文件。
5. **构建项目**:使用 dotnet build 命令构建项目，指定 Release 配置，并设置 SolutionDir 属性以支持项目中可能存在的依赖关系。
6. **打包 NuGet 包**:使用 dotnet pack 命令创建 NuGet 包，同样指定 Release 配置，并将生成的包输出到指定目录。
7. **发布 NuGet 包**:最后，脚本使用 dotnet nuget push 命令将生成的 NuGet 包推送到指定的 NuGet 服务器。

```ps1
$projectSelect = Read-Host -Prompt "Enter the project name 1 is RpBevelNet other is RpBevelNetCompat"
if ($projectSelect -eq 1) {
    $project = "RpBevelNet"
}else {
    $project = "RpBevelNetCompat"
}

# 提示用户输入发布说明
$releaseNote = Read-Host -Prompt "Enter the release note"

# NuGet 包的注册信息 URL
$url = "https://gitlab.roboticplus.com:2025/v3/index.json"
$PackageUrl = "http://gitlab.roboticplus.com:2023/v3/registration/$project/index.json"

# 使用 Invoke-RestMethod 发送 HTTP GET 请求并自动解析 JSON 响应
$response = Invoke-RestMethod -Uri $PackageUrl
# 获取最新版本号
$latestVersion = $response.items[-1].upper
# 输出最新版本号
Write-Host "The latest version of $project is: $latestVersion"

# 将版本号字符串拆分为数组
$versionParts = $latestVersion -split '\.'
# 将修订号（最后一个部分）增加一
$versionParts[-1] = [int]$versionParts[-1] + 1
# 将修改后的部分重新组合为新的版本号
$newVersion = $versionParts -join '.'
# 输出增加后的新版本号
Write-Host "New version is: $newVersion"

# .props 文件路径
$propsFilePath = "./$project/$project.props"
$absolutePath = Resolve-Path $propsFilePath
if (-Not (Test-Path $absolutePath)) {
    Write-Host "File path does not exist: $absolutePath"
    exit
}

# 读取 .props 文件内容
[xml]$propsContent = Get-Content -Path $absolutePath
# 更新 <PackageVersion> 和 <Version> 标签的值
$propsContent.Project.PropertyGroup.PackageVersion = $newVersion
$propsContent.Project.PropertyGroup.Version = $newVersion
$propsContent.Project.PropertyGroup.PackageReleaseNotes = [string]$releaseNote
# 保存更改回 .props 文件
$propsContent.Save($absolutePath)
Write-Host "Updated .props file with new version: $newVersion"

# 设置 .csproj 文件的路径
$csprojPath = "./$project/$project.csproj"
$nugetPackageDir = "./$project/packages"
$propsFilePath = "./"
$absolutePath = Resolve-Path $propsFilePath
# 构建 Release 版本
dotnet build $csprojPath --configuration Release -verbosity:quiet /p:SolutionDir="$absolutePath"
# 打包 Release 版本
dotnet pack $csprojPath --configuration Release /p:SolutionDir="$absolutePath" --output $nugetPackageDir -verbosity:quiet
# 发布 nuget
dotnet nuget push -s "$url" -k RP-NUGET "$nugetPackageDir/$project.$newVersion.nupkg"
```