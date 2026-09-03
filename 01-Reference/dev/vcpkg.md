# C/C++ 项目 vcpkg + CMake 标准化流程指导

本文档提供正式的标准化流程，用于在 C/C++ 项目中**从零开始**配置 vcpkg 和 CMake。核心原则是：**项目的依赖不需要提前手动安装，vcpkg 会在 CMake 配置阶段根据清单文件自动下载并构建所有依赖**。

---

## 目录

1. [前置准备：安装 vcpkg](#1-前置准备安装-vcpkg)
2. [项目目录结构](#2-项目目录结构)
3. [配置 vcpkg 清单文件](#3-配置-vcpkg-清单文件)
4. [配置 CMakeLists.txt](#4-配置-cmakeliststxt)
5. [配置 CMakePresets.json（推荐）](#5-配置-cmakepresetsjson推荐)
6. [构建项目（自动下载依赖）](#6-构建项目自动下载依赖)
7. [添加新依赖的方法](#7-添加新依赖的方法)
8. [版本锁定机制](#8-版本锁定机制)
9. [IDE 集成配置](#9-ide-集成配置)
10. [常见问题排查](#10-常见问题排查)

---

## 1. 前置准备：安装 vcpkg

### 1.1 克隆 vcpkg 仓库

将 vcpkg 安装在**开发环境的中心位置**（如用户目录），以便所有项目共享使用：

```bash
cd ~
git clone https://github.com/microsoft/vcpkg.git
```

### 1.2 执行引导脚本

```bash
cd vcpkg

# Windows
.\bootstrap-vcpkg.bat

# Linux/macOS
./bootstrap-vcpkg.sh
```

### 1.3 设置环境变量（强烈推荐）

设置 `VCPKG_ROOT` 环境变量，使 CMake 能够找到 vcpkg 工具链：

**Windows (PowerShell):**
```powershell
$env:VCPKG_ROOT = "C:\path\to\vcpkg"
$env:PATH = "$env:VCPKG_ROOT;$env:PATH"
```

**Linux/macOS:**
```bash
export VCPKG_ROOT=/path/to/vcpkg
export PATH=$VCPKG_ROOT:$PATH
```

> **提示**：如需持久化，将上述命令添加到 shell 配置文件（`~/.bashrc`、`~/.zshrc`）或 Windows 系统环境变量。

### 1.4 验证安装

```bash
vcpkg --version
```

---

## 2. 项目目录结构

```
my_project/
├── CMakeLists.txt              # 主 CMake 配置
├── CMakePresets.json           # CMake 预设（推荐，可提交）
├── CMakeUserPresets.json       # 用户预设（不提交，含本地路径）
├── vcpkg.json                  # vcpkg 依赖清单【核心文件】
├── vcpkg-configuration.json    # vcpkg 注册表配置（可选）
└── src/
    └── main.cpp
```

---

## 3. 配置 vcpkg 清单文件

### 3.1 初始化清单

在项目根目录执行以下命令，创建清单文件：

```bash
cd /path/to/your/project
vcpkg new --application
```

这会在当前目录生成两个文件：
- `vcpkg.json` - 依赖清单
- `vcpkg-configuration.json` - 注册表配置

### 3.2 编辑 vcpkg.json（核心配置）

清单模式的核心是 `vcpkg.json` 文件。CMake 配置时会**自动读取**此文件并下载所有依赖。

```json
{
    "name": "my-project",
    "version": "1.0.0",
    "dependencies": [
        "fmt",
        "nlohmann-json",
        "zlib"
    ],
    "builtin-baseline": "544a4c5c297e60e4ac4a5a1810df66748d908869"
}
```

**字段说明**：

| 字段 | 必填 | 说明 |
|------|------|------|
| `name` | 是 | 项目名称，小写字母+数字+连字符 |
| `version` | 是 | 项目版本 |
| `dependencies` | 是 | 依赖库列表 |
| `builtin-baseline` | 推荐 | vcpkg 仓库 commit SHA，用于版本锁定 |

> **注意**：`builtin-baseline` 指定了所有依赖的基础版本基线。如果不设置此项，vcpkg 将使用最新版本，可能导致不同环境下构建不一致。

### 3.3 配置 vcpkg-configuration.json（可选）

如果访问默认 vcpkg 仓库较慢，可配置国内镜像或自定义注册表：

```json
{
    "default-registry": null,
    "registries": [
        {
            "kind": "git",
            "repository": "https://gitee.com/JackBoosY/vcpkg.git",
            "baseline": "4497409a47f19db373a410a0efb84eca4747adbf",
            "packages": ["*"]
        }
    ]
}
```

---

## 4. 配置 CMakeLists.txt

### 4.1 关键规则

**最重要的规则**：`CMAKE_TOOLCHAIN_FILE` 必须在第一个 `project()` 调用**之前**设置。因为 CMake 在 `project()` 期间检测编译器和工具链，vcpkg 工具链需要在此之前加载。

### 4.2 标准 CMakeLists.txt 模板

```cmake
# 1. 指定最低版本（必须）
cmake_minimum_required(VERSION 3.18)

# 2. 【关键】在 project() 之前设置工具链文件
#    注意：这里不硬编码路径，通过 CMakePresets.json 或命令行传递
#    此处的注释说明此文件需要配合工具链使用

# 3. 定义项目
project(MyProject VERSION 1.0.0 LANGUAGES C CXX)

# 4. 查找依赖包（vcpkg 会自动提供这些包）
#    注意：这些包不需要提前手动安装！
find_package(fmt CONFIG REQUIRED)
find_package(nlohmann_json CONFIG REQUIRED)
find_package(ZLIB REQUIRED)

# 5. 添加可执行文件
add_executable(my_app src/main.cpp)

# 6. 链接依赖库
target_link_libraries(my_app PRIVATE 
    fmt::fmt
    nlohmann_json::nlohmann_json
    ZLIB::ZLIB
)

# 7. 可选：设置 C++ 标准
target_compile_features(my_app PRIVATE cxx_std_17)
```

### 4.3 CMake 项目中使用 vcpkg 的工作流程

```
cmake 配置
    │
    ▼
检测 vcpkg.json（清单模式）
    │
    ▼
vcpkg 自动读取 dependencies
    │
    ▼
【自动下载并构建所有依赖】
    │
    ▼
依赖安装到 build/vcpkg_installed/
    │
    ▼
find_package() 找到依赖
    │
    ▼
构建项目
```

> **核心要点**：整个过程**不需要**手动执行 `vcpkg install` 命令。CMake 配置时会自动触发 vcpkg 安装。

---

## 5. 配置 CMakePresets.json（推荐）

### 5.1 创建 CMakePresets.json

`CMakePresets.json` 是官方推荐的配置方式，用于定义构建配置预设：

```json
{
    "version": 3,
    "configurePresets": [
        {
            "name": "default",
            "generator": "Ninja",
            "binaryDir": "${sourceDir}/build",
            "cacheVariables": {
                "CMAKE_TOOLCHAIN_FILE": "$env{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake",
                "CMAKE_CXX_STANDARD": 17,
                "CMAKE_CXX_STANDARD_REQUIRED": "ON"
            }
        },
        {
            "name": "release",
            "inherits": "default",
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Release"
            }
        },
        {
            "name": "debug",
            "inherits": "default",
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Debug"
            }
        }
    ],
    "buildPresets": [
        {
            "name": "default",
            "configurePreset": "default"
        }
    ]
}
```

### 5.2 创建 CMakeUserPresets.json（可选，不提交）

用于存储本地特定配置（如自定义 vcpkg 路径），此文件应加入 `.gitignore`：

```json
{
    "version": 3,
    "configurePresets": [
        {
            "name": "local",
            "inherits": "default",
            "environment": {
                "VCPKG_ROOT": "C:/Users/YourName/vcpkg"
            }
        }
    ]
}
```

---

## 6. 构建项目（自动下载依赖）

### 6.1 配置项目

使用预设配置（自动触发 vcpkg 安装）：

```bash
cmake --preset default
```

如果不使用预设，手动指定工具链：

```bash
cmake -B build -S . -DCMAKE_TOOLCHAIN_FILE="$VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake"
```

### 6.2 观察自动下载过程

执行上述命令后，CMake 会自动调用 vcpkg，你会看到类似输出：

```
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Running vcpkg install
Detecting vcpkg root from C:/vcpkg
Computing installation plan...
The following packages will be built and installed:
  * fmt[core]:x64-windows -> 10.1.1
  * nlohmann-json[core]:x64-windows -> 3.11.3
  * zlib[core]:x64-windows -> 1.2.13
...
-- Configuring done
-- Generating done
```

### 6.3 构建项目

```bash
cmake --build build
```

Windows 下指定配置：

```bash
cmake --build build --config Release
```

### 6.4 运行程序

```bash
# Windows
.\build\Release\my_app.exe

# Linux/macOS
./build/my_app
```

---

## 7. 添加新依赖的方法

### 方法一：使用 vcpkg 命令（推荐）

```bash
vcpkg add port <package-name>
```

该命令会自动将依赖添加到 `vcpkg.json` 的 `dependencies` 列表中。

### 方法二：手动编辑 vcpkg.json

直接编辑 `vcpkg.json` 文件，在 `dependencies` 数组中添加：

```json
{
    "dependencies": [
        "fmt",
        "nlohmann-json",
        "zlib",
        "new-package"          // 添加新依赖
    ]
}
```

### 方法三：添加带特性的依赖

某些库支持可选特性（features），使用对象语法声明：

```json
{
    "dependencies": [
        "fmt",
        {
            "name": "opencv",
            "features": ["contrib", "nonfree"]
        },
        {
            "name": "curl",
            "features": ["ssl", "http2"]
        }
    ]
}
```

---

## 8. 版本锁定机制

### 8.1 使用 builtin-baseline（推荐）

`builtin-baseline` 是 vcpkg 官方推荐的版本控制方式。它指定一个 vcpkg 仓库的 commit SHA，作为所有依赖的最低版本基线。

```json
{
    "dependencies": ["fmt", "zlib"],
    "builtin-baseline": "3426db05b996481ca31e95fff3734cf23e0f51bc"
}
```

### 8.2 使用 version>= 约束

为特定包指定最低版本：

```json
{
    "dependencies": [
        {
            "name": "fmt",
            "version>=": "10.1.1"
        },
        "zlib"
    ],
    "builtin-baseline": "3426db05b996481ca31e95fff3734cf23e0f51bc"
}
```

### 8.3 使用 overrides 强制指定版本

当需要强制使用特定版本（如回退到旧版本）时：

```json
{
    "dependencies": ["fmt", "zlib"],
    "builtin-baseline": "3426db05b996481ca31e95fff3734cf23e0f51bc",
    "overrides": [
        {
            "name": "zlib",
            "version": "1.2.8"
        }
    ]
}
```

### 8.4 更新基线

```bash
vcpkg x-update-baseline
```

此命令会将 `builtin-baseline` 更新为当前 vcpkg 实例的最新 commit。

---

## 9. IDE 集成配置

### 9.1 Visual Studio Code

在 `.vscode/settings.json` 中添加：

```json
{
    "cmake.configureSettings": {
        "CMAKE_TOOLCHAIN_FILE": "${env:VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake"
    },
    "cmake.buildDirectory": "${workspaceFolder}/build"
}
```

### 9.2 CLion

在 **Settings > Build, Execution, Deployment > CMake** 的 **CMake options** 中添加：

```
-DCMAKE_TOOLCHAIN_FILE=<vcpkg-root>/scripts/buildsystems/vcpkg.cmake
```

### 9.3 Visual Studio 2022

Visual Studio 内置 vcpkg 支持，可通过菜单 **Project > vcpkg Properties** 配置，或使用 `CMakePresets.json` 方式。

---

## 10. 常见问题排查

### Q1: 出现 "Could not find a package configuration file" 错误

**原因**：vcpkg 尚未安装该包，或 CMake 未正确加载 vcpkg 工具链。

**解决**：
1. 确认 `CMAKE_TOOLCHAIN_FILE` 在 `project()` 之前设置
2. 删除 `build` 目录后重新配置
3. 检查 `vcpkg.json` 中是否声明了该依赖

### Q2: vcpkg 下载依赖很慢

**解决**：配置国内镜像。在 `vcpkg-configuration.json` 中添加 Gitee 镜像：

```json
{
    "registries": [
        {
            "kind": "git",
            "repository": "https://gitee.com/JackBoosY/vcpkg.git",
            "baseline": "4497409a47f19db373a410a0efb84eca4747adbf",
            "packages": ["*"]
        }
    ]
}
```

### Q3: 为什么工具链文件必须在 project() 之前？

**原因**：CMake 在 `project()` 调用期间检测编译器和工具链。vcpkg 工具链文件需要在此之前加载，以便正确设置搜索路径和库查找逻辑。

### Q4: 如何处理不同平台的依赖？

vcpkg 使用**三元组（Triplet）**来管理不同平台的构建。可以设置 `VCPKG_TARGET_TRIPLET` 变量，例如 `x64-windows`、`x64-linux`、`arm64-osx`。

### Q5: 如何清理 vcpkg 缓存？

```bash
vcpkg remove --outdated
vcpkg remove --purge <package-name>
```

---

## 附录：完整示例

### 示例项目文件

**vcpkg.json:**
```json
{
    "name": "hello-vcpkg",
    "version": "1.0.0",
    "dependencies": ["fmt"],
    "builtin-baseline": "544a4c5c297e60e4ac4a5a1810df66748d908869"
}
```

**CMakeLists.txt:**
```cmake
cmake_minimum_required(VERSION 3.18)
project(HelloVcpkg)

find_package(fmt CONFIG REQUIRED)
add_executable(hello src/main.cpp)
target_link_libraries(hello PRIVATE fmt::fmt)
```

**CMakePresets.json:**
```json
{
    "version": 3,
    "configurePresets": [
        {
            "name": "default",
            "binaryDir": "${sourceDir}/build",
            "cacheVariables": {
                "CMAKE_TOOLCHAIN_FILE": "$env{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake"
            }
        }
    ]
}
```

**src/main.cpp:**
```cpp
#include <fmt/core.h>

int main() {
    fmt::print("Hello, vcpkg!\n");
    return 0;
}
```

### 构建命令

```bash
export VCPKG_ROOT=/path/to/vcpkg
cd hello-vcpkg
cmake --preset default
cmake --build build
./build/hello
```

---

## 参考资源

- [vcpkg 官方文档](https://vcpkg.io/)
- [CMake 官方文档](https://cmake.org/documentation/)
- [vcpkg GitHub 仓库](https://github.com/microsoft/vcpkg)