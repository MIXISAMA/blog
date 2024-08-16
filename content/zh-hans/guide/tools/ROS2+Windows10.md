---
title: ROS2+Windows10
date: 2024-04-12T18:10:00+08:00
categories: 
- 实用工具
tags:
- ROS2
- Windows
---

ROS2相对于ROS1，在消息层使用DDS使得消息传输更加稳定，这对于工业环境是十分必要的。ROS2也对更多平台做了支持，之前ROS在Windows上是不支持的，本文也是在Windows10上来搭建ROS2环境。ROS2官方文档使用Chocolatey来安装依赖环境，ROS2中很多包也是直接写死了Chocolatey默认安装路径下的依赖，这样很占用C盘空间。本文手动安装依赖环境，对于部分库存在的情况，只需要配置好相应的环境变量以及软链接即可。

<!-- more -->

本文没有特殊说明则在管理员模式的PowerShell下进行操作。

## 1. Chocolatey

> 2024年8月15日：ROS的依赖包多的没边，而windows没有包管理工具，如果不用Chocolatey真的是一种折磨。后面我也没时间研究ROS2了，就算研究也不会用Windows跑这玩意，纯折磨。

虽然本文不用Chocolatey，但是ROS2中一些包写死了Chocolatey的路径，直接使用依赖包软链接，不如在非C盘装一个Chocolatey，然后把Chocolatey软链接到C盘上。如果后续用到Chocolatey，仍然有很好的扩展性。

```ps1
$env:ChocolateyInstall = "E:\software\chocolatey"
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
New-Item -ItemType SymbolicLink --Path C:/ProgramData/chocolatey -Target E:\software\chocolatey
```

## 2. Python

ROS2强制使用`C:\Python38`路径作为Python路径，这点很离谱。目前也不清楚ROS2是否支持除了3.8以外的Python版本，如今2024年很多库已经结束了对3.8的支持，所以急需验证ROS2对Python3.9以上的支持程度。本文使用最后一个二进制发布版本[Python3.8.10](https://www.python.org/downloads/release/python-3810/)作为ROS2使用的Python。

本文安装在`E:\python\Python38_only_for_ROS`下。没有对该Python配置Path环境变量。

为了让ROS2找到该Python，创建一个软链接。

```ps1
New-Item -ItemType SymbolicLink -Path C:\Python38 -Target E:\python\Python38_only_for_ROS
```

然后安装一些ROS2需要的依赖包

```ps1
$env:Path = "C:\Python38\Scripts;C:\Python38;"
python -m pip install -U pip setuptools==59.6.0
python -m pip install -U catkin_pkg cryptography empy importlib-metadata lark==1.1.1 lxml matplotlib netifaces numpy opencv-python PyQt5 pillow psutil pycairo pydot pyparsing==2.4.7 pyyaml rosdistro
```

如果ROS2支持Python3.9以上版本，使用conda或者pyenv+poetry等虚拟环境管理软件是一个很好的选择，待验证后再加虚拟环境。

## 3. Visual C++ Redistributables

本文使用ROS官方手册推荐的[Visual C++ Redistributable Packages for Visual Studio 2013](https://www.microsoft.com/zh-cn/download/details.aspx?id=40784)

## 4. OpenSSL

ROS2使用OpenSSL1.1.1，OpenSSL的兼容性很差，最好不要用旧的或新的。官方在2024年4月11日下架了OpenSSL1.1.1的二进制安装版，需要找找归档或手动编译安装，OpenSSL对于交叉编译条件也很苛刻，最好都使用MSVC来编译。

本文OpenSSL安装在`E:\OpenSSL-Win64`，然后设置好环境变量。

* `OPENSSL_CONF`: `E:\OpenSSL-Win64\bin\openssl.cfg`
* `PATH`: `E:\OpenSSL-Win64\bin`

## 5. Visual Studio

个人原因，本人真的是不爱用「宇宙最强IDE」的Visual Studio，所以本文直接使用[VS生成工具](https://visualstudio.microsoft.com/zh-hans/downloads/)。

找到`用于 Visual Studio 的工具` - `Visual Studio 2022 生成工具`，下载安装即可。本文安装到了`E:\Microsoft Visual Studio`。

安装过程中选择`Desktop development with C++`，取消选择`CMake tools`，后续单独安装CMake。

## 6. OpenCV

OpenCV向后兼容性还可以，本文直接安装最新版Windows二进制发布包[OpenCV 4.9.0](https://opencv.org/releases/)。安装位置为`E:\opencv`。

设置好环境变量：

* `OpenCV_DIR`: `E:\opencv\opencv\build`
* `Path`: `E:\opencv\opencv\build\x64\vc16\bin`

## 7. CMake

直接下载[最新版](https://cmake.org/download/)安装即可。然后配置好环境变量：

* `Path`: `E:\software\CMake\bin`

## 8. TinyXML-usestl

下载[源码](https://sourceforge.net/projects/tinyxml/)，直接开编。PlatformToolset根据自己的VS工具集来设置，我这里只有v143。

```ps1
cd tinyxml
MSBuild tinyxml.sln ^
  -target:tinyxml ^
  /p:OutDir=E:\resource\tinyxml\ ^
  /p:Configuration=Release ^
  /p:Platform=Win32 ^
  /p:PlatformToolset=v143
```

urdf库调用这个tinyxml，但是路径是写死的，绝了。需要调整路径然后创建软链接。

```ps1
mkdir E:\resource\tinyxml\lib
mkdir E:\resource\tinyxml\include
Move-Item -Path E:\resource\tinyxml\tinyxml.* -Destination E:\resource\tinyxml\lib\
Copy-Item -Path tiny*.h -Destination E:\resource\tinyxml\include\
New-Item -ItemType SymbolicLink -Path C:\ProgramData\chocolatey\lib\tinyxml-usestl -Target E:\resource\tinyxml
```

## 8. TinyXML-2

下载[源码](https://github.com/leethomason/tinyxml2/releases/tag/10.0.0)，先看一下cmake编译选项。

```ps1
cd tinyxml2-10.0.0
mkdir build
cd build
cmake -LH ..
```

设置不编译测试，Release类型，以及安装路径。然后编译安装。

```ps1
cmake .. `
  -D BUILD_TESTING=OFF `
  -D tinyxml2_BUILD_TESTING=OFF `
  -D CMAKE_CONFIGURATION_TYPES=Release `
  -D CMAKE_INSTALL_PREFIX=E:\resource\tinyxml2
cmake --build . --config Release
cmake --install .
```

## 9. Qt5

[Qt](https://www.qt.io/offline-installers)需要注册账号才能安装。安装后设置环境变量：

* `Qt5_DIR`: `E:\software\Qt5.12.12\Tools\QtCreator\bin`
* `QT_QPA_PLATFORM_PLUGIN_PATH`: `E:\cppsource\ros2-windows\bin\platforms`

`QT_QPA_PLATFORM_PLUGIN_PATH`这个环境变量中的值在本文还没有提到，可以在下载ROS2后再设置。ROS2用到这个路径下的`qwindows.dll`插件，但Qt中的与ROS2用到的符号不一致，ROS2二进制发布包中自带了一个可用的`qwindows.dll`插件，直接用ROS2中的就好。

## 10. Graphviz

rqt_graph需要[Graphviz](https://graphviz.org/download/)，安装以后记得设置环境变量：

* `Path`: `E:\software\Graphviz\bin`

## 11. ROS2

本文使用ROS2 LTS版本[Humble](https://github.com/ros2/ros2/releases)，直接解压就算安装完了，本文解压到了`E:\cppsource\ros2-windows`。

## 12. DDS

这个可选但很必要，这个是和ROS1最大的区别，使用[DDS](https://www.rti.com/free-trial)作为消息层可以保证节点之间消息传递的有效性。这个需要填写教育许可证免费试用。

本文安装在`E:\software\rti_connext_dds-7.3.0`。

## 13. Invoke-CmdScript

PowerShell真难用，不能直接使用MSVC的环境变量设置脚本。万幸有人写了一个[PowerShell模块](https://github.com/Wintellect/WintellectPowerShell)，里面有一些工具函数可以直接拿来用。使用下面的命令安装模块。

```ps1
Install-Module -Name WintellectPowerShell
```

利用其中的Invoke-CmdScript就可以一行命令设置MSVC环境了。

## 14. 例子

对于每个PowerShell控制台，都需要初始化环境。

```ps1
# 激活DDS环境
E:\software\rti_context_dds-7.3.0\resource\scripts\rtisetenv_x64Win64VS2017.bat
# 激活ROS2环境
E:\cppsource\ros2-windows\local_setup.ps1
# 激活MSVC环境
Invoke-CmdScript "E:\Microsoft Visual Studio\2022\BuildTools\VC\Auxiliary\Build\vcvarsamd64_x86.bat"
# 设置Python路径
$env:Path = "C:\Python38\Scripts;C:\Python38;" + $env:Path
$env:PYTHONPATH += "C:\Python38\Lib\site-packages"
# 如果前面的依赖不想设置系统/用户环境变量，可以在下面设置
# $env:Path += "xxx"
```

然后尝试运行这个例子。

```ps1
ros2 run demo_nodes_cpp talker
```

再打开一个PowerShell控制台，然后初始化环境，运行下面这个例子。

```ps1
ros2 run demo_nodes_py listener
```

## 15. 其他依赖

其他依赖包含：

* asio
* bullet
* cunit
* eigen

本文还没有对其他依赖进行编译和安装，但目前使用没有什么问题，遇到问题再说。
