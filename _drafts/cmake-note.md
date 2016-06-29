---
title: cmake笔记
date: 2016-06-29 08:05:27
categories:
tags:
---

# 1. 介绍 #

CMake (cross platform make) 是一个跨平台的自动构建工具，可以做为autoconf+automake的[替代方案](http://blog.csdn.net/cnsword/article/details/7542696)，用过的都说好。

# 2. 示例 #

示例项目结构如下：

```plaintext
myproject/
|-- CMakeLists.txt
|-- libs
|   |-- libthirdpart0.a
|   `-- libthirdpart1.so
|-- main
|   |-- CMakeLists.txt
|   |-- inc
|   `-- src
`-- mods
    |-- CMakeLists.txt
    |-- mod0
    |   |-- CMakeLists.txt
    |   |-- inc
    |   `-- src
    `-- mod1
        |-- CMakeLists.txt
        |-- inc
        `-- src
```

主程序目录为`main`，依赖的两个第三方库放在`libs`，两个功能模块放在`mods`，功能模块做为共享库供主程序使用。主程序和模块共享库构建输出目录分别为`bin`和`lib`。

下面来填写项目中的5个`CMakeLists.txt`。

```cmake
# myproject/CMakeLists.txt

# project name
project(myproj)

# build type: Debug Release
set(CMAKE_BUILD_TYPE Debug)
set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -Wall -Werror -g")
set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -Wall -Werror")

add_subdirectory(main)
add_subdirectory(mods)
```

```cmake
# myproject/main/CMakeLists.txt

include_directories(${PROJECT_SOURCE_DIR}/main/inc)

aux_source_directory(src MAIN_SRCS)
find_library(THRPART0 thirdpart0 ${PROJECT_SOURCE_DIR}/libs)
find_library(THRPART1 thirdpart1 ${PROJECT_SOURCE_DIR}/libs)

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
add_executable(myproj ${MAIN_SRCS})
target_link_libraries(myproj mod0 mod1 ${THRPART0} ${THRPART1})
```

```cmake
# myproject/mods/CMakeLists.txt

set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)
add_subdirectory(mod0)
add_subdirectory(mod1)
```

```cmake
# myproject/mods/mod0/CMakeLists.txt

include_directories(${PROJECT_SOURCE_DIR}/mods/mod0/inc)
aux_source_directory(src MOD0_SRCS)
add_library(mod0 ${MOD0_SRCS})
```

```cmake
# myproject/mods/mod1/CMakeLists.txt

include_directories(${PROJECT_SOURCE_DIR}/mods/mod1/inc)
aux_source_directory(src MOD1_SRCS)
add_library(mod1 ${MOD0_SRCS})
```

# 3. 命令和变量 #

