---
title: "VScode配置MSVC编译环境"
tags: [vscode, C/Cpp, Visual Studio, CN]
style: fill # fill / border
color: danger # primary / secondary / success / danger / warning / info / light / dark
description: 不想安装VS？VScode重度用户？一步到位用VScode编译MSVC程序
---

## 安装Visual Studio生成工具

进入[VS下载界面](https://visualstudio.microsoft.com/zh-hans/downloads/)，下拉找到**Visual Studio 2022 生成工具**下载。安装过程只选择C++生成工具即可。

## 通过Developer cmd启动VScode

安装完成后，为了防止污染，MSVC大量的环境变量不会自动导入系统，因此不能直接打开vscode。

VS提供了控制台，用于启动带环境变量的开发环境。
在程序列表搜索“Developer Command Prompt”打开即可启动控制台。
在控制台输入

```batch
code
```

进入VScode按`F1`输入`terminal`打开终端，输入`$env:include`，如果出现环境目录例如：

```batch
C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC\Tools\MSVC\14.43.34808\include;C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC\Auxiliary\VS\include;C:\Program Files (x86)\Windows Kits\10\include\10.0.22621.0\ucrt;C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\um;C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\shared;C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\winrt;C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\cppwinrt
```

说明环境导入成功！但是以后每次启动都需要通过VS的Developer控制台输入`code`。

## 将环境目录导入VScode设置

为了实现直接自动导入环境变量，需要将上述环境变量导入VScode的`include path`中。

VScode按`F1`输入`Open User Settings (JSON)`,将上一步`$env:include`输出内容改为JSON格式：

1. 分号改为逗号，每一个目录加双引号
2. 每个目录并换行
3. `\`双写

将修改后的路径加入文件中`C_Cpp.default.systemIncludePath`例如：

```json
    "C_Cpp.default.systemIncludePath": [
        "C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\VC\\Tools\\MSVC\\14.43.34808\\include",
        "C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\VC\\Auxiliary\\VS\\include",
        "C:\\Program Files (x86)\\Windows Kits\\10\\include\\10.0.22621.0\\ucrt",
        "C:\\Program Files (x86)\\Windows Kits\\10\\include\\10.0.22621.0\\um",
        "C:\\Program Files (x86)\\Windows Kits\\10\\include\\10.0.22621.0\\shared",
        "C:\\Program Files (x86)\\Windows Kits\\10\\include\\10.0.22621.0\\winrt",
        "C:\\Program Files (x86)\\Windows Kits\\10\\include\\10.0.22621.0\\cppwinrt"
    ]
```

## CMake配置

大型项目使用VScode内置的`tasks`编译过于繁琐，且不便自定义和量产，因此我使用CMake编译。

首先在[CMake官网](https://cmake.org/download/)下载最新的二进制文件并安装。

然后在VScode安装`CMake Tools`扩展。

在左侧工具栏点击`CMake`Batch，在`Configure`一栏选择`Visual Studio生成工具`（如果没有点击`[Scan for kits]`扫描）

在项目文件夹下创建`CMakeLists.txt`文件，例如

```cmake
# 设置最低 CMake 版本
cmake_minimum_required(VERSION 3.10)

# 设置项目名称
project(flight_sim VERSION 0.1 LANGUAGES C)

set(CMAKE_C_STANDARD 17)
set(CMAKE_C_FLAGS /source-charset:utf-8)

aux_source_directory(src SOURCES)

# 添加可执行文件
add_executable(${PROJECT_NAME} ${SOURCES})

target_include_directories(${PROJECT_NAME} PUBLIC
    include
)

```

然后打开源代码，在下方工具栏使用`Build`图标编译并运行即可。
