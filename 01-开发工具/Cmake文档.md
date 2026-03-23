### 构建命令
```
# cmake -s cmake文件路径 -B 构建目录
	cmake -S . -B build-cmake
```
### 基本配置
```
# 版本要求
	cmake_minimum_required(VERSION 3.28)
# 定义工程名称
	project(muduo C CXX)
# 定义可执行文件
	add_executable(可执行程序名 源文件名称)
```

### 变量定义set

#### 定义变量
```useage
SET(VAR [VALUE] [CACHE TYPE DOCSTRING []])

# 方式一：直接指定文件，文件之间使用空格间隔
set(SRC_LIST add.c div.c mult.c sub.c main.c)

# 方式二：各个源文件之间使用分号 ; 间隔
set(SRC_LIST add.c div.c mult.c sub.c main.c)

add_executable(app ${SRC_LIST})
```
#### 源代码版本指定
```
# 使用set命令指定
set(CMAKE_CXX_STANDARD 11) #c++11
set(CMAKE_CXX_STANDARD 14) #c++14
set(CMAKE_CXX_STANDARD 17) #c++17

# cmake命令时指定宏的值
cmake CMakeLists.txt文件路径 -DCMAKE_CXX_STANDARD=11
```
#### 可执行程序输出路径
```
set(BIN_DIR /home/dingjr/cmake-bin)
set(EXECUTABLE_OUTPUT_PATH "${BIN_DIR}")
```

### 搜索文件
#### 查找路径下的所有源文件
```
aux_source_directory(< dir > < variable >)
add_executable(<target>  <variable>)
```