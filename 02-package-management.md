# 软件安装管理（Package Management）



**Linux 软件和 Windows 软件有什么区别**

在Windows中，安装软件通常需要下载官网安装包，然后双击安装

Linux 不太一样。Ubuntu 使用的是软件包管理器（Package Manager），它负责：下载软件、安装软件、管理依赖、更新软件、卸载软件

比如安装 Git：

Windows：下载安装包→双击安装

Ubuntu：只需要一条命令

```bash
sudo apt install git
```

**什么是 apt**

Advanced Package Tool（高级软件包工具），Ubuntu 的软件管理工具

**为什么需要 sudo**

普通用户没有权限修改：

```text
/usr/bin
/usr/lib
/etc
```

但是安装软件通常需要修改它们

所以需要管理员权限

------

## apt update：更新软件列表

Ubuntu 有一个软件仓库：repository（仓库），里面记录：软件名、版本、下载地址、依赖关系

就像这个样子：

```text
Ubuntu软件目录
│
├── git 2.53
├── python3.14
├── vim
└── gcc
```

执行：

```bash
sudo apt update
```

做的是：

```text
服务器
  ↓
下载最新软件列表
  ↓
保存到本机
```

## apt upgrade：更新已经安装的软件

upgrade（升级），例如：

现在：

```text
git 版本 2.52
```

仓库：

```text
git 版本 2.53
```

执行：

```bash
sudo apt upgrade
```

会更新已经安装且由 apt 管理的软件包，更新到到 Ubuntu 软件源中的最新可用版本，但不会安装新软件，也不会跳到软件官网的最新版

如果软件没有安装，即使仓库里有，也不会被apt upgrade安装

先apt update，再apt upgrade

## apt install：安装软件

```bash
sudo apt install 软件名
```

例如：

安装 tree：

```bash
sudo apt install tree
```

安装 vim：

```bash
sudo apt install vim
```

安装 Git：

```bash
sudo apt install git
```

一次安装多个：

```bash
sudo apt install git vim tree
```

## apt remove：删除软件

```bash
sudo apt remove 软件名
```

但是配置文件可能保留

## apt purge：清除软件

```bash
sudo apt purge 软件名
```

比 remove 更彻底，配置文件也会删除

## apt list：查看软件

查看软件：

```bash
apt list
```

查看已安装的软件：

```bash
apt list --installed
```

## apt show：查看软件信息

```bash
apt show 软件名
```

会显示：软件介绍、版本、大小、依赖

## apt search：搜索软件

```bash
apt search 软件名
```

搜索相关软件

------

**软件安装在哪里**

Windows默认会安装在C:\Program Files

而Linux软件安装的位置比较分散

- 可执行文件：/usr/bin
- 库文件：/usr/lib
- 配置文件：/etc
- 用户自己的程序：~/.local/bin
- 第三方大型软件：/opt

**依赖（Dependency）**

例如现在要安装：

```bash
sudo apt install gcc
```

但是 gcc 需要：

```text
gcc
 |
 ├── libc
 ├── make
 └── binutils
```

apt 会自动安装gcc需要的这些东西

**python3-pip：Python 包管理器**

用于安装和卸载python第三方库，管理python库版本，例如：

```
pip install 包名    （安装某个包）
pip list       （查看已经安装的python包）
pip show 包名        （查看某个包的信息）
pip uninstall 包名    （卸载某个包）
```

**python3-venv：Python 虚拟环境工具**

不同项目需要的python库及其版本可能不同，如果把所有包都安装到系统python，可能会出现版本冲突

所以引入了虚拟环境（virtual environment），用于创建一个独立的python环境，让每个项目都拥有自己的python解释器、pip、python包，互不影响

而pip、venv这些属于系统工具，安装一次即可，之后每个项目都可以使用（系统里只有一个 pip工具，但是每创建一个 .venv，里面都会自动放一个属于这个虚拟环境的 pip）

例如：

- 创建虚拟环境

进入项目目录：

```bash
cd ~/projects/python-study
```

创建：

```bash
python3 -m venv .venv
```

解释一下：

```
python3
│
调用 Python

-m
│
运行模块(module)

venv
│
创建虚拟环境

.venv
│
虚拟环境目录名字
```

创建后：

```
python-study
│
└── .venv
```

里面包含：

```
.venv
├── bin
│   ├── python
│   └── pip
├── lib
└── include
```

- 激活虚拟环境

```bash
source .venv/bin/activate
```

激活后终端会发生变化，从原来的 lazimian233@ubuntu:~/projects/python-study$

变成(.venv) lazimian233@ubuntu:~/projects/python-study$，说明当前使用的是项目自己的 Python

检查：

```bash
which python
```

正常：

```
/home/lazimian233/projects/python-study/.venv/bin/python
```

而不是：

```
/usr/bin/python3
```

激活虚拟环境后，终端输入的 python、pip 都会优先使用当前 .venv 中的版本

- 退出虚拟环境

~~~bash
deactivate
~~~

退出虚拟环境后，python 和 pip 会重新指向系统默认环境，不再使用当前 .venv 中安装的第三方库

- 删除虚拟环境

直接删除整个 .venv 文件夹

~~~bash
rm -rf .venv
~~~

**最后放一张有虚拟环境的目录结构示意图：**

~~~
Ubuntu
│
├── Python3
├── pip（安装 Python 第三方库）
└── venv（创建虚拟环境）
        │
        ▼
~/projects
│
├── python-study
│   ├── main.py
│   ├── README.md
│   └── .venv
│       ├── bin
│       │   ├── python          （当前虚拟环境的 Python 解释器）
│       │   ├── python3
│       │   ├── pip             （当前虚拟环境的 pip）
│       │   ├── pip3
│       │   ├── activate        （激活虚拟环境）
│       │   ├── activate.csh
│       │   └── activate.fish
│       │
│       ├── lib
│       │   └── python3.x
│       │       └── site-packages
│       │           ├── numpy
│       │           ├── pandas
│       │           ├── requests
│       │           └── ...
│       │
│       ├── include
│       │
│       └── pyvenv.cfg
│
└── deep-learning
    ├── train.py
    ├── model.py
    └── .venv
        ├── bin
        │   ├── python
        │   ├── python3
        │   ├── pip
        │   ├── pip3
        │   ├── activate
        │   ├── activate.csh
        │   └── activate.fish
        │
        ├── lib
        │   └── python3.x
        │       └── site-packages
        │           ├── torch
        │           ├── transformers
        │           ├── accelerate
        │           └── ...
        │
        ├── include
        │
        └── pyvenv.cfg
~~~

