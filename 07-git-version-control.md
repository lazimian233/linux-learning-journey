# Git（版本控制）



**Git 是什么**

分布式版本控制系统（Distributed Version Control System，DVCS）

记录变化，而不是单纯保存文件

**Git 的三个重要区域**

```text
工作区
Working Directory   平时的项目文件在这里编辑

        |
        | git add    准备提交哪些修改

        ▼

暂存区
Staging Area        保存下次准备提交的内容

        |
        | git commit  提交哪些修改

        ▼

版本库
Repository         提交，形成一个版本
```

------

## git init：仓库初始化

Initialize，把普通文件夹变成 Git 仓库，例如：

之前是普通的文件夹：

```text
git-test/

├── main.py
```

执行：

```bash
git init
```

变成了：

```text
git-test/

├── .git/
├── main.py
```

多了一个 .git，它是Git保存版本信息的隐藏目录，里面保存着历史记录、分支、提交信息等

不要手动修改 .git，它相当于Git 的数据库

## git status：查看仓库状态

创建文件：

```bash
touch main.py
```

然后执行：

```bash
git status
```

可能看到：

```text
On branch main   在哪个分支上
 
Changes to be committed:    还没有提交的修改
  modified:   README.md
 
Changes not staged for commit:   还没有暂存的修改
  modified:   main.py

Untracked files:   还没有被git管理的新文件
  test.py
```

如果想简洁显示状态，可以：

~~~bash
git status -s
~~~

可能得到

```
 M main.py
A  README.md
?? test.py
```

它的格式是：

```
XY filename
```

两个位置代表两个区域的状态

```
第一列 X     第二列 Y

暂存区状态     工作区状态
```

- 空：无变化
- M：有修改
- A：被添加
- ？？：未追踪

## git add：加入暂存区

有时候可能修改了多个文件，但只准备提交其中一部分，就把准备提交的文件add到暂存区里，暂时不提交的文件就继续留在工作区

执行：

```bash
git add main.py
```

现在这个文件的状态从Untracked变成了Staged，也就是进入暂存区了

再执行：

```bash
git status
```

会看到：

```text
Changes to be committed:

new file: main.py
```

意思是有一个新文件等待提交

注意，git add 不会保存版本历史，git commit才会

一些例子：

- 添加单个文件：

```bash
git add main.py
```

- 添加多个文件：

```bash
git add main.py login.py
```

- 添加当前目录所有修改（包括新建文件、修改文件、删除文件等）：

```bash
git add .
```

一个文件git add后依然能修改，只不过要再次git add才会保存修改

## git commit：提交

Git里的提交不是上传到 GitHub，而是把暂存区里的文件保存在本地版本库里，也就是说commit只发生在本地

准确来讲，Git 保存的不是整个项目文件，而是文件状态的变化记录

commit会提交当前分支对应的暂存区里的所有内容

还是之前的例子：

```text
git-test/

└── main.py
```

查看状态：

```bash
git status
```

看到：

```text
Untracked files:

main.py
```

第一步，加入暂存区：

```bash
git add main.py
```

再次：

```bash
git status
```

会看到：

```text
Changes to be committed:

new file: main.py
```

第二步，提交：

```bash
git commit -m "add main.py"
```

这里的-m意思是message（提交说明），后面双引号里的内容就是具体的提交说明，这样以后查看历史就知道每次修改了什么

## git log：查看提交历史

执行：

```bash
git log
```

可能看到：

```text
commit 8f3a9c2d...

Author: user

Date:

add main.py
```

这里面最重要的是：

```text
commit 8f3a9c2d
```

它是commit ID

每个commit都有唯一编号，例如8f3a9c2d7e4a，称为Commit Hash（提交哈希）

类似于身份证号码，Git通过这个ID找到对应版本

为什么 commit ID 是一串乱码？因为它来自Hash（哈希），简单理解就是：Git 通过Hash算法对文件内容、作者、时间、提交信息等计算一个数字指纹，只要内容变化，Hash就变化。所以Git 可以判断文件有没有被修改

如果不想看那么多的信息，想简化一些，可以：

```bash
git log --oneline
```

只输出commit ID和提交说明：

```text
a83f91c add login
92bd123 init project
```

------

**提交后再修改会发生什么**

现在已经commit了main.py

然后再修改它

查看：

```bash
git status
```

显示：

```text
modified: main.py
```

意思是工作区和最后一次 commit 不一样。

想提交新版本，就重新：

```bash
git add main.py

git commit -m "update message"
```

**一个完整流程**

```bash
修改代码

↓

git status  检查变化

↓

git add    选择保存

↓

git commit  提交版本

↓

继续修改
```

------

## git diff：查看两个区域之间的差别

例如，现在：

```python
print("Hello")
```

add到暂存区，然后再修改：

```python
print("Hello Git")
```

执行：

```bash
git diff
```

输出类似：

```diff
- print("Hello")      删除旧内容
+ print("Hello Git")  添加新内容
```

默认比较的是工作区和暂存区的内容

如果要比较暂存区和本地版本库的内容，需要：

```bash
git diff --staged
```

或者：

```bash
git diff --cached
```

## git restore：恢复文件

如果修改还没有add，执行：

```bash
git restore main.py
```

如果修改已经 add 了，想把它从暂存区退回工作区，执行：

```bash
git restore --staged main.py
```

---

**为什么需要Branch（分支）**

假设正在开发网站，当前版本可以稳定运行，突然要求增加一个新功能

如果直接修改当前版本的话，有网站崩掉的风险，所以可以创建一个分支

```text
main

     \
      \
       feature
```

在这个feature分支里进行开发，完成以后再合并回main

一句话：分支就是一条独立的开发路线

注意，分支不是复制一份文件夹，而是Git 内部创建一个新的指针，例如：

现在：

```text
A---B---C

       ↑

      main
```

创建分支：

```text
A---B---C

       ↑
       main
       feature
```

两个分支暂时指向同一个地方

继续开发：

```text
A---B---C---D

       ↑   ↑
       |   |
     main feature
```

实际上main 没动，feature 往前走

在哪个分支上提交，哪个指针就往前走

main代表产品的稳定版本，feature代表实验室里的新功能

可以同时有多个分支

------

## git branch：创建和查看分支

执行：

```bash
git branch feature
```

创建feature分支：

```text
feature
```

执行：

```bash
git branch
```

查看分支：

```text
* main
  feature
```

其中 * 表示当前所在分支

## git switch：切换分支

执行：

```bash
git switch feature
```

切换到feature分支

## git merge：把分支合并回来

开发完成后，需要把 feature 合回 main

例如，现在：

```text
A---B---C  main
     \
      D---E  feature
```

切换回main：

```bash
git switch main
```

执行：

```bash
git merge feature
```

结果：

```text
A---B---C---F

     \     /
      D---E
```

Git 创建了新的合并节点：F

这样，不同人可以同时开发不同的功能，最后进行合并

另外，merge在自动合并的过程中可能会产生冲突，例如：如果两个分支修改了同一部分代码，Git无法判断保留哪个版本，需要人工解决冲突

## git branch -d：删除分支

合并完成后，被合并的分支就可以删除了

~~~
git branch -d feature
~~~



---

**什么是远程仓库**

实际开发中，代码不会只存在自己的电脑里

比如公司的项目、开源项目、多人协作等，都会需要远程仓库（Remote Repository）

远程仓库可以理解为放在另一台服务器上的 Git 仓库

```text
你的电脑

Git Repository


        │
        │ 网络
        ▼


GitHub服务器

Git Repository
```

**Git 和 GitHub 的区别**

- Git：一个版本控制工具，安装在本机，管理代码版本
- GitHub：提供 Git 仓库托管服务的网站，保存Git仓库，提供协作

------

## git remote：查看远程仓库

现在我们有本地仓库，但是Git 不知道远程仓库在哪里，需要告诉它

执行：

```bash
git remote
```

如果没有远程仓库的话，就没有输出

想看得更详细：

```bash
git remote -v
```

可能得到：

```text
origin https://github.com/user/project.git (fetch)

origin https://github.com/user/project.git (push)
```

这里的origin是远程仓库的名字，默认就叫origin

## git remote add：添加远程仓库

命令：

```bash
git remote add origin 地址
```

例如：

```bash
git remote add origin https://github.com/user/project.git
```

意思是告诉 Git，这个地址叫 origin

如果要关联多个远程仓库，需要给它们起不同的名字，把origin换成其他的什么

## git push：上传代码到远程仓库

执行：

```bash
git push
```

会把本地分支中远程没有的 commit 推送过去

第一次 push时需要指定分支，例如：

```bash
git push -u origin main
```

这里的 -u（upstream）意思是“建立关联”

以后再推送就直接：

```bash
git push
```

Git 就知道要推到哪里了

一个本地仓库可以关联多个远程仓库，一个远程仓库也可以被多个本地目录clone

如果这个本地仓库关联了多个远程仓库，那么每次推送时都要写清楚远程仓库和分支，不能省略

但是如果某个分支已经设置了upstream，之后仍然可以默认推送到对应的upstream

## git pull：下载更新

当别人修改了代码、GitHub有新版本，而本机上的还是旧版本时，就需要更新

```bash
git pull
```

pull实际可以拆成两步：

```text
git fetch

+

git merge
```

也就是：先下载、再合并

## git clone：克隆远程仓库

把一个远程 Git 仓库完整复制到本地

复制的不只是代码文件，还包括：Git历史记录、分支信息、commit记录

```bash
git clone 仓库地址
```

例如，GitHub有一个：

```text
project
```

执行：

```bash
git clone https://github.com/user/project.git
```

本地就会有：

```text
project/

├── main.py
├── README.md
└── .git/
```

这里有 .git，说明它已经是一个完整 Git 仓库，不是普通的文件夹

克隆时，git会自动设置远程仓库名字为origin

所以，进入项目：

```bash
cd project
```

执行：

```bash
git remote -v
```

会看到类似：

```text
origin  https://github.com/user/project.git
```

也就是说，git clone 相当于帮你完成了以前需要手动做的：

```bash
git init

git remote add origin 地址

git fetch
```

------

**clone 后怎么参与开发**

第一步：clone

```bash
git clone https://github.com/user/project.git
```

得到本地仓库

第二步：进入项目

```bash
cd project
```

第三步：创建功能分支

例如，开发登录功能：

```bash
git switch -c feature-login
```

第四步：修改代码

例如：

```text
login.py
```

第五步：提交

```bash
git add .

git commit -m "add login feature"
```

第六步：上传

```bash
git push
```

**git clone 和下载 ZIP 的区别**

GitHub 上通常有：

```
Code
 ├── Clone
 └── Download ZIP
```

下载zip得到的是代码文件，没有 .git

**git clone 和 git pull 的区别**

git clone是在没有这个项目时复制整个仓库，比如第一次加入项目

git pull是已经有了这个项目，需要获取最新变化，比如每天同步团队代码

**通过SSH方式 clone**

除了 HTTPS：

```bash
git clone https://github.com/user/project.git
```

还有SSH：

```bash
git clone git@github.com:user/project.git
```

**什么是SSH Key**

每次git push时，GitHub 怎么知道：你是谁？

一种方式是使用用户名 + 密码，但是这样不太安全，所以现在更常用的是SSH Key

可以把它理解为一对钥匙：

```text
私钥 Private Key

+

公钥 Public Key
```

公钥上传到 GitHub，而私钥留在自己电脑里

连接时，GitHub 会验证：你拥有对应私钥吗？如果有，才允许访问。

它使用的是SSH协议，所以叫SSH Key

**怎样查看已有的 SSH Key**

Linux：

```bash
ls ~/.ssh
```

可能看到：

```text
id_rsa
id_rsa.pub
```

其中：

```text
.pub

是公钥
```

如果没有，说明还没生成

**关于 .gitignore**

有时候，项目里有些文件不应该提交，比如文件太大/包含密码/不需要上传等情况

于是，可以在项目目录里创建：

```text
.gitignore
```

里面写上不需要被git管理的文件名，比如这样：

```text
.venv/
.env
```

目录：

```text
project/

├── main.py
├── .venv/
├── .env
└── .gitignore
```

执行：

```bash
git status
```

就不会看到：

```text
.venv
.env
```

但是注意，这样只能忽略没有被 Git 管理的文件，如果已经add和commit了，再写入 .gitignore就不会生效了，因为Git 已经在跟踪它

需要先取消跟踪：

```bash
git rm --cached 文件名
```
