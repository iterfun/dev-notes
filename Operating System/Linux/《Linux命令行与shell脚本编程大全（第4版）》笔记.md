# 1 初识 Linux Shell

## 深入探究 Linux 内核

内核主要负责 4 种功能：

- 系统内存管理
- 软件程序管理
- 硬件设备管理
- 文件系统管理

### 系统内存管理

交换空间（swap space）

页面（page）

换出（swapping out）

换入（swapping in）

### 软件程序管理

Linux操作系统称运行中的程序为进程。
内核创建了第一个进程来启动系统中所有其他进程。

目前最流行的两种 init 进程实现：SysVinit(旧) 和 systemd(新)。
SysVinit 初始化方法使用运行级（runlevel）的概念来决定启动哪些进程。
systemd方法通过将事件与单元文件（unitfile）链接来决定运行哪些进程。
systemd方法将单元文件划归为目标（target）。

### 硬件设备管理

Linux 系统将硬件设备视为一种特殊文件，称为设备文件（device file），分为 3 种：字符设备文件、块设备文件、网络设备文件。
Linux 会为系统的每个设备创建一种称为“节点”的特殊文件。与设备的所有通信都通过节点文件进行。

### 文件系统管理

Linux 内核采用虚拟文件系统（virtual file system）作为和各种文件系统交互的接口（Linux 内核与其他类型文件系统之间通信的标准接口）。

## GNU 实用工具

GNU coreutils 软件包 由 3 部分构成：

- 文件实用工具
- 文本实用工具
- 进程实用工具

shell 脚本

## 桌面环境

### X Window 软件：

- X.org(旧)
- Wayland(新)

### KDE Plasma 桌面（类 Microsoft Windows 图形化）

### GNOME 桌面

# 2 走进 Linux

命令行界面（command line interface，CLI）

# 3 Bash Shell 基础命令

`man`: 访问手册页

分页程序（pager）
