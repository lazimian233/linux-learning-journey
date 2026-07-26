# 文件和目录操作（File & Directory Operations）



**Linux的文件结构**

~~~
/
├── home
│   └── lazimian233（我的用户名）
│       └── projects
│
├── usr
├── bin
├── etc
├── tmp
└── var
~~~

Linux 里：

- 文件 = file
- 文件夹/目录 = directory
- 当前所在位置 = working directory

路径（path）分为绝对路径和相对路径：

- 绝对路径：从根目录/开始，例如/home/lazimian233/projects
- 相对路径：相对于当前目录而言，例如当前位置是/home/lazimian233，写projects即可

一些特殊路径：

- **~**  表示家目录，也就是/home/lazimian233
- **/**  表示根目录
- **.**  表示当前目录
- **..**  表示父目录，也就是上一级目录
- /root  表示root用户（管理员）的家目录，root是Linux中权限最高的用户/超级用户

------

*以下命令，如果没有专门标注的话，都是在当前的目录下进行的；此外也可以通过指定路径来操作其他目录*

## pwd——查看当前目录

print working directory，例如：

~~~bash
pwd
~~~

输出：

~~~
/home/lazimian233/projects/python-study
~~~

说明当前所在的位置是/home/lazimian233/projects/python-study，用于定位，输出的是绝对路径

## ls——查看目录内容

list(列出，也就是列出当前目录里的文件和文件夹)，例如：

```bash
ls
```

输出：

```
hello.py
```

说明当前目录下有 hello.py 文件

Linux命令后面可以加参数，命令和参数之间有一个空格：

- **-a**  全称all,意思是显示全部，因为默认会隐藏像 .venv这样的以“ . ”开头的文件夹或文件
- **-l**  全称long listing format，意思是是长格式列表，显示详细信息
- **-al** 显示所有文件的详细信息

## cd——进入目录

change directory（改变目录，也就是切换当前所在位置），例如：

当前：

```
/home/lazimian233
```

输入：

```bash
cd projects
```

进入：

```
/home/lazimian233/projects
```

路径可以是相对路径也可以是绝对路径

~~~bash
cd -        回到刚才的位置
cd ~        进入家目录
cd ..       进入父目录
cd /        进入根目录
~~~

## mkdir——创建目录

make directory，例如：

```bash
mkdir test
```

会创建 test/

创建多级目录需要加一个 -p（parents，表示父目录），例如：

```bash
mkdir -p projects/deep-learning
```

如果projects不存在，它会自动创建：

```
projects
└── deep-learning
```

## touch——创建文件

touch在Linux里表示“更新文件时间戳”，后来常用于创建空文件，例如：

```bash
touch hello.py
```

会创建hello.py

如果文件已经存在，touch不会清空文件内容，只会更新时间

## rm——删除

remove，例如：

删除文件：

```bash
rm hello.py
```

删除目录：

```bash
rm -r test
```

这里的 **-r**（recursive，递归）意思是连里面的东西一起处理，会把test文件夹里的内容也给一起删除了

强制递归删除目录：

~~~bash
rm -rf test
~~~

这里的 **-f**（force，强制）会强制删除目录以及里面的所有内容，这个命令要谨慎使用

rm默认不会进入回收站，通常删除了就很难恢复了

## cp——复制

copy，例如：

复制文件：

```bash
cp a.py b.py
```

前一个是源文件，后一个是目标文件，也就是把a复制到b

复制目录：（此时new目录不存在）

```bash
cp -r old new
```

目录里面还有东西，需要递归复制，所以要加-r

如果后面写的是已经存在的目录名，则会把前面写的文件或目录复制到后面这个目录之下

## mv——移动/改名

move，例如：

```bash
mv hello.py ~/projects
```

把 hello.py 移动到 projects/ 文件夹里

改名字：

```bash
mv old.py new.py
```

Linux没有单独的 rename 命令，所以移动到一个新名字就相当于改名了

## tree——查看目录结构

常用：

```bash
tree -L 2
```

**-L**（level，层级）表示显示多少层，这里是显示两层

## nano——用终端编辑器打开文件

简单的编辑器，可以对文件内容进行编辑

打开文件：

```bash
nano hello.py
```

保存：Ctrl + O

退出：Ctrl + X

## code——用vscode打开

打开当前目录：

```bash
code .
```

打开某个文件：

```bash
code hello.py
```

---

**关于Windows磁盘**

WSL 会把 Windows 磁盘挂载到 /mnt下，例如：

Windows：

```
C:\Users\16377\Desktop
```

在 WSL 中对应：

```
/mnt/c/Users/16377/Desktop
```

所以进入桌面：

```bash
cd /mnt/c/Users/16377/Desktop
```