# C++ Development Setup

参考:
- https://hackingcpp.com/cpp/tools/beginner_dev_setup.html

## Linux

```bash
sudo apt install -y g++, build-essential
```

## Windows

### VS Code

安装 [Visual Studio Code](https://code.visualstudio.com/)

安装 `C/C++`扩展 `WSL`扩展

Settings: 搜索 `Clang_format_fallback Style` 设置为 `{ BasedOnStyle: Microsoft, BreakBeforeBraces: Attach }`

### WSL (推荐)

https://learn.microsoft.com/en-us/windows/wsl/install

### MSYS2

#### 安装 [MSYS2 (Mininal SYStem 2)](https://www.msys2.org/)

![image.png](https://raw.githubusercontent.com/jiaahui/Pictures/main/MSYS2_subenv.webp)

进入 `UCRT62` 子环境，即启动 `ucrt64.exe`

#### 切换镜像

寻找 `/etc/pacman.d/mirrorlist.ucrt64` 文件，调整 `ustc` 或 `tuna` 地址的优先级

#### 同步更新工具

```bash
pacman -Syu
```

#### 安装 `mingw-w64` 工具链

```bash
pacman -S mingw-w64-ucrt-x86_64-toolchain
```

我们安装的编译器位于 `ucrt64/bin/` 下，把这个目录下的 `gcc.exe` 加入环境变量

如果需要多文件编译，需要额外安装 `CMake` 和 `ninja`

```bash
pacman -S mingw-w64-ucrt-x86_64-cmake mingw-w64-ucrt-x86_64-ninja
```

#### 第三方库的安装和使用

首先搜索是否存在需要的第三方库

```bash
pacman -Ss
```

然后使用 `pacman` 安装即可

一般第三方库可以直接使用，但是也存在一些例外，例如 `opencv` 使用自己独立的 `include` 系统，所以在使用时需要通过 `-isystem <path>` 手动指定

#### 外部终端使用 MSYS2 命令

新建 msys.cmd 并添加到环境变量

```batch
@echo off
if "%1"=="" goto noarg
%MSYS2%\msys2_shell.cmd -defterm -no-start -here -msys2 -c "%*"
goto :eof
:noarg
%MSYS2%\msys2_shell.cmd -defterm -no-start -here -msys2
```

这样，如果只输入 `msys2` 进入 `MSYS2`, 如果输入 `msys2 <msys2 cli>` 则执行 `MSYS2` 内部命令
