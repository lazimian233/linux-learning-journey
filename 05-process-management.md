# 进程管理（Process Management）



**程序和进程**

- 程序（Program）：存放在磁盘上的可执行文件/脚本/相关资源文件等等


例如：

```text
/usr/bin/python3
/usr/bin/git
/bin/ls
```

这些都只是文件，它们静静地躺在硬盘里，还没有运行

- 进程（Process）：正在运行的程序

当你执行一个程序时，Linux 会：把程序加载到内存、分配运行资源、开始执行

这时候程序就变成了进程

**Linux 可以同时运行很多进程**

打开 Ubuntu时，其实已经有很多程序正在运行，例如：

```text
bash
systemd
python
ssh
```

Linux 一直都在管理很多进程

```text
内存（Memory）

├── bash
├── python
├── vim
├── ssh
├── code
└── ...
```

它们每一个都是一个进程

**PID（Process ID）**

每一个进程都有一个Process ID（编号），简称PID

它的作用是：唯一标识一个正在运行的进程

例如：

要结束一个python程序，不是结束：

```text
python
```

而是结束：

```text
PID = 1020
```

因为可能同时运行多个python程序，它们名字一样，都是python，但编号不一样

------

## ps：查看进程

process status（查看当前进程状态），例如：

```bash
ps
```

可能得到：

```text
PID   TTY      TIME      CMD

2130  pts/0    00:00:00  bash
2450  pts/0    00:00:00  ps
```

解释每一列：

| 列   | 含义         |
| ---- | ------------ |
| PID  | 进程编号     |
| TTY  | 终端         |
| TIME | CPU 使用时间 |
| CMD  | 运行的程序   |

ps会去linux内核读取系统所有的进程信息，但显示哪些进程由ps的参数决定，默认情况下只显示当前终端（TTY）关联的进程

如果要显示整个系统的进程，就要执行ps aux

如果当前终端正被一个前台进程占用，就无法在该终端执行 ps。此时可以打开另一个终端，执行ps aux；或者先将前台进程暂停/放到后台，再执行ps

## ps aux：查看所有进程

真正最常用的是：

```bash
ps aux
```

它可以看到整个系统，例如：

```text
USER       PID  %CPU  %MEM COMMAND

root         1   ...
root       500   ...
user      2030   bash
user      2501   python
user      3100   code
```

## top：实时查看进程

如果把ps比作拍照，看一下就结束

那么top就像是直播，会不断刷新，看到的CPU、Memory、PID、COMMAND等会一直变化

要退出的话按q即可

为什么叫 top？因为它默认会把CPU 占用最高的进程放在最上面

另一个更好的进程查看工具：htop

## kill：结束进程

例如，一个进程的PID是3245，要结束这个进程：

```bash
kill 3245
```

意思是向3245发送“请求结束”的信号，程序可以正常清理资源后退出

不是直接强制结束

------

**什么是前台进程（Foreground Process）**

例如，输入：

```bash
python
```

屏幕变成：

```text
Python 3.12.3 ...
>>>
```

这时候就不能再输入其他命令了，因为Python 正在占用这个终端

```text
Terminal
    │
    ▼
python（前台运行）
```

前台进程就是当前正在使用终端的进程，终端只能专心和这个程序交互

**什么是后台进程（Background Process）**

有时候程序不需要一直交互，例如：

```bash
python train.py
```

它可能会训练很久，如果一直占用终端，就很不方便

所以Linux 可以让它在后台运行，就像这样：

```bash
python train.py &
```

这样终端就会马上回来，可以继续执行其他命令，而那个程序仍在运行

```text
Terminal
│
├── 后台：python train.py
│
└── 当前终端
      │
      ├── ls
      ├── pwd
      └── git
```

最后的这个

```text
&
```

会告诉 Shell：启动以后，不要占用当前终端

例如：

```bash
sleep 300 &
```

可能输出：

```text
[1] 5623
```

这里的[1]不是PID，而是：Job 编号（作业编号）

后面的5623才是：PID

------

## jobs：查看当前shell管理的作业

例如，运行：

```bash
sleep 300 &
```

输入：

```bash
jobs
```

可以看到

而输入：

```bash
ps
```

也可以看到

注意，由于job是当前shell维护的一层记录，并且当有前台进程占用终端时无法执行jobs，所以jobs查看的是当前 Shell 管理的作业，包括后台运行（Running）和暂停（Stopped）的作业

------

**进程和作业**

jobs和ps都能看到 sleep 300 &，那它们到底有什么区别？

- 进程（Process）是操作系统管理的对象
- 作业（Job）是当前 Shell 为了方便用户管理前台/后台任务而维护的一层"记录"

```text
Linux 内核
    │
    ├── Process A（PID 1234）
    ├── Process B（PID 5678）
    └── Process C（PID 9012）

            ▲
            │
      Bash（Shell）
            │
      Job 1 → PID 1234
      Job 2 → PID 5678
```

所以：

- ps 是问 Linux 内核："现在有哪些进程？" 
- jobs 是问 当前 Shell："我管理着哪些作业？"

这也是为什么：新开一个终端，ps还能看到系统里的进程；但 jobs通常是空的，因为那个终端没有创建过任何后台作业

前台/后台是shell区分的概念，而在Linux内核看来，它们都只是普通的进程

------

## fg：恢复前台

例如，后台正在运行：

```bash
sleep 300 &
```

执行：

```bash
fg
```

它就会变成：

```text
sleep 300
```

终端再次被占用

如果有多个后台任务，例如：

```text
[1]
[2]
[3]
```

可以：

```bash
fg %2
```

这样就会恢复第二个

## Ctrl+Z：暂停进程

例如，前台正在运行：

```bash
python
```

按下，Ctrl + Z，输出：

```text
[1]+ Stopped
```

注意，进程并没有结束，只是暂停了

## bg：继续运行

按下Ctrl+Z暂停某个程序以后，执行：

```bash
bg
```

程序就会继续运行，只不过会放在后台

## Ctrl+C：结束进程

可以这样记：

Ctrl+C——Cancel——结束 

Ctrl+Z——Freeze——暂停

这两个针对的都是当前终端的前台进程

而kill可以操作任何符合权限的进程，但必须知道PID

## nohup：使程序在终端关闭后继续运行

通常情况下，关闭终端后，Shell 没了，后台任务也会一起结束

所以Linux 提供了nohup，也就是no hang up（不要因为终端关闭而挂起程序）

例如：

```bash
nohup python server.py &
```

这样，关闭终端后，程序会继续运行

------

**终端和Shell**

- 终端（Terminal）：一个提供输入和输出界面的程序（窗口）

只负责输入命令和输出结果，本身不会理解各种命令是什么意思

- Shell：真正理解命令的

终端打开以后，会启动一个 Shell，比如说Bash就是一个Shell

例如：

```bash
echo $SHELL
```

可能得到：

```text
/bin/bash
```

说明当前运行的是Shell是Bash，它才是真正理解命令的人

也就是说，Terminal 是窗口，Bash 是窗口里面运行的程序

```text
┌────────────────────────────┐
│ Terminal（终端窗口）         │
│ 提供输入输出界面              │
└──────────────┬─────────────┘
               │
               ▼
┌────────────────────────────┐
│ Shell（比如Bash）           │
│ 解释命令、管理作业            │
└──────────────┬─────────────┘
               │
      ┌────────┴────────┐
      │                 │
      ▼                 ▼
Foreground Job      Background Job
（前台作业）          （后台作业）
      │                 │
      └────────┬────────┘
               ▼
┌────────────────────────────┐
│ Linux Kernel               │
│ 管理 Process（PID）         │
└────────────────────────────┘
```

```text
Terminal（终端）
        │
        ▼
Shell（Bash）
        │
        ├── 解释命令
        ├── 管理前台/后台 Job
        ├── jobs
        ├── Ctrl+Z
        ├── Ctrl+C
        ├── fg
        └── bg
                │   Shell 是用户与 Linux 内核之间的桥梁。
                ▼
Linux Kernel
        │
        ├── 创建 Process
        ├── 分配 PID
        ├── ps
        ├── top
        └── kill
```
