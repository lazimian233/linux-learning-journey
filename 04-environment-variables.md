# 环境变量（Environment Variables）



**什么是环境、什么是环境变量**

例如：现在打开 Ubuntu

```bash
lazimian233@ubuntu:~$
```

此时系统已经知道很多信息：

```text
当前用户是谁？
当前目录在哪？
默认语言是什么？
终端是什么？
HOME目录在哪？
```

这些信息就是：当前运行环境

系统把这些信息保存起来，这些保存的信息就是：环境变量

所以环境变量可以理解成：系统保存的一组变量

------

## env：查看环境变量

```bash
env
```

它会列出当前所有环境变量

```text
HOME=/home/lazimian233
USER=lazimian233
LANG=zh_CN.UTF-8
PATH=/usr/local/bin:/usr/bin:/bin
```

## echo $:只查看某一个环境变量

注意，echo本身和环境变量没有关系，它表示的是“输出后面的内容”，比如echo hello就会输出hello

而这里的**$**表示“取变量的值”，所以shell会先把变量名替换成实际的值，再输出，例如：

```bash
echo $HOME
```

输出：

```text
/home/lazimian233
```

------

**什么是PATH（路径）**

这里不是 /home 这种路径，而是“命令搜索路径”

Linux中的绝大多数命令，本质上都是一个可执行程序

例如：

输入git，Linux 怎么知道：

应该运行：

```text
/usr/bin/git
```

而不是：

```text
/home/test/git
```

因为PATH里面已经记录好了去哪些目录找命令

它是所有命令共用的一个”搜索清单”，每一个命令都会按照它去搜索，而不是单独针对某个命令

**PATH长什么样**

查看：

```bash
echo $PATH
```

例如：

```text
/usr/local/bin:/usr/bin:/bin:/home/lazimian233/.local/bin
```

这里的“**：**”是分隔符

```text
PATH

↓

第一项
/usr/local/bin

第二项
/usr/bin

第三项
/bin

第四项
/home/lazimian233/.local/bin
```

可以画成：

```text
PATH
│
├── /usr/local/bin
├── /usr/bin
├── /bin
└── ~/.local/bin
```

这四项是找到命令的四种路径

**怎样搜索命令**

例如，输入python

系统不会整个在硬盘搜索，而是按照 PATH一个一个目录找

```text
PATH
│
├── /usr/local/bin
│      │
│      └── 有没有 python？
│
├── /usr/bin
│      │
│      └── 有没有 python？
│
├── /bin
│
└── ~/.local/bin
```

找到了：

```text
/usr/bin/python3
```

立即执行这条命令，停止搜索

**关于python解释器和python脚本**

python，也就是python解释器，是一个可执行程序

而像hello.py这样的python脚本，通常它只是一个普通的文本文件，不是可执行程序（但经过配置后也可以作为可执行脚本运行）

当输入：

```bash
python hello.py
```

真正发生的是：

```text
Shell
│
▼
执行 python（可执行程序）
│
▼
python 打开 hello.py
│
▼
读取和解释
│
▼
CPU执行
```

注意：真正运行的是python 而不是hello.py

这就是为什么：

```bash
python hello.py
```

第一个参数是：

```text
python
```

第二个参数才是：

```text
hello.py
```

与C的区别：C 经过gcc编译之后生成的程序本身就是可执行文件，不需要再经过解释器

------

## which：运行的是哪一个程序

例如，输入：

```bash
which python
```

得到：

```text
/home/lazimian233/projects/python-study/.venv/bin/python
```

其实这里的which做的事情就是：

```text
查看 PATH

↓

依次搜索

↓

返回第一个找到的程序
```

所以可以理解为：Which executable?（运行的是哪一个程序？）

------

**激活 .venv 后发生了什么**

执行：

```bash
source .venv/bin/activate
```

不是Python 被替换了，而是PATH 被修改了

原来：

```text
PATH

/usr/local/bin
/usr/bin
/bin
```

激活后变成：

```text
PATH

/home/lazimian233/projects/python-study/.venv/bin
/usr/local/bin
/usr/bin
/bin
```

虚拟环境中的python被放到了最前面，所以它会第一个被搜索到，然后执行，不会再往下搜索其他的

而退出虚拟环境时，PATH 就恢复了