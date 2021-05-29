---
title: VSCode 下 C\C++ 开发环境配置
cover: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/vscoe.749spwxeqw80.png
tags:
  - C
categories:
  - C\C++
date: 2021-05-24 21:15:36

---

参考视频：

{% link 哔哩哔哩视频, https://www.bilibili.com/video/BV13K411M78v, https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/bilibiliIcon.svg %}

## 开发环境搭建

+ MinGW-w64 编译器 [官网下载链接](http://www.mingw-w64.org/doku.php/download)
+ CMake工具 [官网下载链接](https://cmake.org/download/)

将下载的压缩包解压，并将 解压后的文件夹的 bin 文件夹目录放到环境变量中。 

VSCode 插件，在插件中搜索下载

+ C\C++：智能提示、debug 和代码浏览
+ CMake：CMake 的语法支持
+ CMake Tools：CMake 的扩展支持

---

CMake Tools 安装遇到的坑

问题描述：

CMake Tools 版本为 1.7.3，报错代码如下

```
Bad CMake executable . Is it installed or settings contain the correct path (cmake.cmakePath)? 
```

原因是 CMake Tools 没有找到正确的 CMake 安装路径 

解决方法：在设置中指定 cmake.exe 的文件地址。具体方法是打开设置（快捷键 Ctrl + 逗号）搜索 cmake.cmakePath 可以看到 Name/path of the CMake executable to use. 默认为 cmake 将其设置自己安装 cmake.exe 文件地址，例如我的设置是 E:\Program\Develop\Cmake\bin\cmake.exe。

参考解决地址：https://github.com/microsoft/vscode-cmake-tools/issues/685

## 基于 g++ 命令

### 编译单文件

```cpp
#include <iostream>
using namespace std;

void swap(int &a, int &b) {
    int temp;
    temp = a;
    a = b;
    b = temp;
}

int main() {
    int val1 = 10;
    int val2 = 20;
    cout << "Before swap:" << endl;
    cout << "val1 = " << val1 << endl;
    cout << "val2 = " << val2 << endl;

    swap(val1, val2);
    cout << "After swap:" << endl;
    cout << "val1 = " << val1 << endl;
    cout << "val2 = " << val2 << endl;

    return 0;
}
```

这里顺便写段实例代码，打开命令行 VSCode 默认快捷键为 Ctrl 加反引号。

```
E:\Project\CodeBase\C\VSCode>g++ .\main.cpp

E:\Project\CodeBase\C\VSCode>.\a.exe
Before swap:
val1 = 10   
val2 = 20   
After swap: 
val1 = 20   
val2 = 10  
```

这里会默认生成 a.exe 可执行文件，通过命令行可以运行。

```
E:\Project\CodeBase\C\VSCode> g++ -g .\main.cpp -o swap
```

通过上一段命令可以生成名为 swap 的可调试的可执行文件

![VSCode配置launch](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/VSCode配置launch.json.png)

选择 g++.exe - Build and debug active file ，会在目录 .vscode 下自动生成 launch.json 文件夹，同时产生了与 cpp 同名的 .exe 文件用于断点调试 

### 编译多文件

将上述代码拆分成多个文件如下：

```cpp
// many_main.cpp
#include <iostream>
#include "many.h"

using namespace std;

int main() {
    int val1 = 10;
    int val2 = 20;
    cout << "Before swap:" << endl;
    cout << "val1 = " << val1 << endl;
    cout << "val2 = " << val2 << endl;

    swap(val1, val2);

    cout << "After swap:" << endl;
    cout << "val1 = " << val1 << endl;
    cout << "val2 = " << val2 << endl;

    return 0;
}
```

```cpp
// many_swap.cpp
#include "many.h"

void swap(int &a, int &b) {
    int temp;
    temp = a;
    a = b;
    b = temp;
}
```

```cpp
// many.h
void swap(int &a, int &b);
```

通过 g++ 编译多文件

```
g++ -g .\many_main.cpp .\many_swap.cpp -o many_swap 
```

如果使用 VSCode 默认生成的 launch.json 进行调试会出错，这里需要进行配置，修改 `program` 属性为 g++ 生成的可调式的可执行文件名，并注释 `preLaunchTask`，这里因为自己使用 g++ 命令编程完成，不在需要 VSCode 进行编译

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "g++.exe - Build and debug active file",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}\\many_swap.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "miDebuggerPath": "E:\\Program\\Develop\\MinGW\\mingw64\\bin\\gdb.exe",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            // "preLaunchTask": "C/C++: g++.exe build active file"
        }
    ]
}
```

## 基于 Cmake 

在主目录中新建 CMakeLists.txt

```
project(SWAP)

add_executable(cmake_swap many_main.cpp many_swap.cpp)
```

使用 Shift + Ctrl + P 调出配置搜索，搜索 CMake:Configure 选择 mingw 

之后会自动生成 build 文件夹

```
E:\Project\CodeBase\C\VSCode>cd build

E:\Project\CodeBase\C\VSCode\build>cmake ..
-- Configuring done
-- Generating done
-- Build files have been written to: E:/Project/CodeBase/C/VSCode/build

E:\Project\CodeBase\C\VSCode\build>mingw32-make.exe
```

就会在 build 文件夹中生成 cmake_swap.exe （根据 CMakeLists.txt 配置生成）

如果电脑中安装了 VS 可以会调用微软的 MSVC 编译器，第一次使用时用

```
cmake -G "MinGW Makefiles" ..
```

来代替

```
cmake ..
```

## 修改 launch.json 和 tasks.json

launch.json 需要关注的点为：

+ `program`：可执行文件的路径
+ `preLaunchTask`：对应 tasks.json 的 `label` 属性的值

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "g++.exe - Build and debug active file",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}\\build\\cmake_swap.exe",
            // 调试时需要添加的参数
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "miDebuggerPath": "E:\\Program\\Develop\\MinGW\\mingw64\\bin\\gdb.exe",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "C/C++: g++.exe build active file"
        }
    ]
}
```

tasks.json 用于调试之前的编译工作指令，需要关注的点为：

+ `args`：多文件需要指定文件名
+ `label`：与 launch.json 的 `preLaunchTask` 对应

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: g++.exe build active file",
            "command": "E:\\Program\\Develop\\MinGW\\mingw64\\bin\\g++.exe",
            "args": [
                "-g",
                "${file}",
                "-o",
                "${fileDirname}\\${fileBasenameNoExtension}.exe"
            ],
            "options": {
                "cwd": "${fileDirname}"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "Task generated by Debugger."
        }
    ],
    "version": "2.0.0"
}
```

对于 CMake 文件的自动配置 tasks.json 如下

```json
{
    "version": "2.0.0",
    "options": {
        "cwd": "${fileDirname}/build"
    },
    "tasks": [
        {
            "type": "shell",
            "label": "cmake",
            "command": "cmake",
            "args": [
                ".."
            ]
        },
        {
            "label": "make",
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "command": "mingw32-make.exe",
            "args": []
        },
        {
            "label": "Build",
            "dependsOn": [
                "cmake",
                "make"
            ]
        }
    ]
}
```