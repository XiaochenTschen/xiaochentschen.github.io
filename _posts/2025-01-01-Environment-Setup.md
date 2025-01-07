---
layout: post
title:  "Dev Env Setup"
tags:
  - embedded system
  - stm32
hero: /assets/img/embedded_system.jpeg
overlay: blue
---

当使用开源软件进行嵌入式软件开发时，开发环境应该如何设置。这里将以stm32nucleo开发板为例，详细讲解如何搭配好开发环境。
{: .lead}
<!–-break-–>

完整的开发环境包含以下几部分：
+ 代码编辑工具：VS Code
+ 代码编译工具: ARM GNU Toolchain
+ 第三方库: libopencm3
+ 文件烧录和Debug工具: ST-LINK

下面将对每一部分分别进行介绍。
<hr/> 

### 软件编译工具

使用开源的Arm GNU Toolchain。可以从官网下载并解压工具包，将arm-gnu-toolchain文件夹放到/usr/local/文件夹下，并保证该路径已经添加到PATH变量中。

### Debugger

开发板自带ST-LINK，相应的驱动软件可以从GitHub自行下载编译安装：
```bash
git clone https://github.com/stlink-org/stlink.git
cd stlink

cmake -DCMAKE_BUILD_TYPE=Release .
make
sudo make install
sudo ldconfig
```
为了确保不需要 root 权限就能访问 ST-Link 设备，你可能需要安装 UDEV 规则。ST-Link仓库中通常包含了一个 UDEV 规则文件，你可以将它复制到 /etc/udev/rules.d/ 目录下：
```bash
sudo cp config/udev/rules.d/49-stlinkv*.rules /etc/udev/rules.d/
sudo udevadm control --reload
```
连接开发板到电脑，使用`st-info`指令检测开发板是否被识别。

使用`st-util`指令可以启动一个debuger服务器，这个时候如果在新的terminal中使用`arm-none-eabi-gdb ./firmware.elf`指令调试程序，可以检测debugger是否能正常工作。

如果Ubuntu系统较新，使用的是libncursesw.so.6，但是rm-none-eabi-gdb需要libncursesw.so.5。解决方法是创建一个libncursesw.so.5软链接指向libncursesw.so.6.
{: .notice}

### 第三方库

使用[[libopencm3](https://github.com/libopencm3/libopencm3)]，源码可以从GitHub下载并编译使用。

也可以下载[[示例代码](https://github.com/libopencm3/libopencm3-examples)]查看如何使用库提供的函数。

### VS Code

需要安装Cortex-Debug扩展，并准备相应的配置文件。

```json
// c_cpp_properties.json
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [
                "STM32F4",
                "__ARM_ARCH_7EM__"
            ],
            "compilerPath": "/usr/local/arm-gnu-toolchain/bin/arm-none-eabi-gcc",
            "cStandard": "c99",
            "cppStandard": "c++14",
            "intelliSenseMode": "gcc-arm"
        }
    ],
    "version": 4
}

// tasks.json
{
    "version": "2.0.0",
    "tasks": [
      {
        "type": "shell",
        "label": "build_debug",
        "options": {
          "cwd": "app"
        },
        "command": "make bin",
        "problemMatcher": "$gcc"
      }
    ]
}

// launch.json
{
    "configurations": [
      {
        "name": "ST-Link: Debug Application",
        "cwd": "${workspaceFolder}/app",
        "executable": "./firmware.elf",
        "servertype": "stutil",
        "request": "launch",
        "type": "cortex-debug",
        "device": "STM32F401xD",
        "runToEntryPoint": "main",
        "interface": "swd",
        "preLaunchTask": "build_debug"
      },
      {
        "name": "ST-Link: Attach to Application",
        "cwd": "${workspaceFolder}/app",
        "executable": "./firmware.elf",
        "servertype": "stutil",
        "request": "attach",
        "type": "cortex-debug",
        "device": "STM32F401xD",
        "runToEntryPoint": "main",
        "interface": "swd"
      },
    ]
}
```