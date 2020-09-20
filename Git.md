## Git

### 1、Git 概述

#### 1.1、Git 与 SVN 对比

> SVN

`SVN` 是**集中式**版本控制系统，版本库是集中放在**中央服务器**的，而开发人员开发工作的时候，用的都是自己的电脑，所以首先要从中央服务器下载最新的版本，然后开发，开发完后，需要把自己开发的代码提交到中央服务器

集中式版本控制工具缺点：

- 服务器单点故障（一旦服务器宕机，整个项目将无法正常工作）
- 容错性差

> Git

`Git` 是**分布式**版本控制系统，分为两种类型仓库：**本地仓库**和**远程仓库**

- 本地仓库：开发人员自己电脑上的 `Git` 仓库
- 远程仓库：在远程服务器上的 `Git` 仓库

#### 1.2、Git 工作流程

<img src="C:\Users\Shinelon\AppData\Roaming\Typora\typora-user-images\image-20200920102605650.png" alt="image-20200920102605650" style="zoom: 80%;" />

1. 从远程仓库中克隆代码到本地仓库
2. 从本地仓库中 `checkout` 代码然后进行代码修改
3. 在提交前先将代码提交到暂存区
4. 提交到本地仓库，本地仓库中保存修改后的各个历史版本
5. 修改完成后，需要和团队成员共享代码时，将代码 `push` 到远程仓库

### 2、Git 常用命令

#### 2.1、环境配置

当安装 `Git` 后，首先要做的事情就是设置**用户名称**和 **`email` 地址**，但这不是用来认证的，只是每次 `Git` 提交都会使用该用户信息

> 设置用户信息

- `git config --global user.name "用户名称"`
- `git config --global user.email "用户email"`

通过上述命令设置的信息都会保存在 `~/.gitconfig` 文件中

> 查看配置信息

- `git config --list`
- `git config user.name`

#### 2.2、获取 Git 仓库

> 在本地初始化一个 Git 仓库

1. 在终端进入一个目标目录
2. 执行命令`git init`
3. 如果在当前目录中看到 `.git` 文件夹（此文件夹为隐藏文件夹）则说明 `Git` 仓库创建成功

> 从远程仓库克隆

1. 在终端进入一个目标目录
2. 执行命令 `git clone 克隆目标地址`，如：`git clone https://github.com/oddii/notes.git`

#### 2.3、Git 工作目录下文件的两种状态

`Git` 工作目录下的文件存在两种状态：

- `Untracked` 未跟踪（未被纳入版本控制）
- `Tracked` 已跟踪（被纳入版本控制）
  - `Unmodified` 未修改状态
  - `Modified` 已修改状态
  - `Staged` 已暂存状态

这些文件的状态会随着我们执行 `Git` 的命令发生变化

#### 2.4、本地仓库操作

##### 2.4.1、git status

查看文件状态，使用 `git status -s` 会使提示信息更简洁

简写状态提示：

- 未跟踪状态：`??`
- 已修改，未暂存状态：红色的 `M`
- 已修改，已暂存状态：绿色的 `M`
- 添加后已暂存状态：红色的 `A`

##### 2.4.2、git add [filename]

将文件加入暂存区

- `filename`：文件名

##### 2.4.3、git reset HEAD [filename]

将暂存区的文件取消暂存，取消暂存后，文件会回到暂存前的状态，如果之前是未跟踪，则回到未跟踪状态，其它状态同理

- `filename`：文件名

##### 2.4.4、git commit -m [message]

将暂存区的文件修改提交到本地仓库

- `message`：更新信息/日志信息

##### 2.4.5、git rm [filename]

将文件删除并将此次操作加入暂存区

- `filename`：文件名

##### 2.4.6、将文件添加至忽略列表

在工作目录中创建一个名为：`.gitignore` 文件，下面是一个实例：

```gitignore
# no .a files
*.a

# but do track lib.a, even though you're ignoring .a files above
!lib.a

# only ignore the TODO file in the current directory, not subdir/TODO
/TODO

# ignore all files in the build/ directory
build/

# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt

# ignore all .pdf files in the doc/ directory
doc/**/*.pdf
```

##### 2.4.7、git log

查看日志操作，如果日志信息太多，可以使用回车查看下一条，或输入 `q` 退出查看

#### 2.5、远程仓库操作

##### 2.5.1、git remote

查看远程仓库，列出指定的每一个远程服务器的简写，如果已经克隆了该仓库，那么至少应该能看到 `origin`，这是 `Git` 克隆的仓库服务器的默认名字

查看更多信息：

- `git remote -v`：查看关联的远程仓库 `fetch` 和 `push`
- `git remote show origin`：查看关联远程仓库的详细信息

##### 2.5.2、git remote add [shortname] [url]

添加远程仓库

- `shortname` ：分支简称，通常可随意填写，通常为 `origin`
- `url`：远程仓库 `url` 地址，需要填写一个已创建的远程仓库 `url` 地址

##### 2.5.3、git clone [url]

克隆远程仓库，默认配置下远程 `Git` 仓库中的每一个文件的每一个版本都将被拉取下来

- `url`：远程仓库 `url` 地址，需要填写一个已创建的远程仓库 `url` 地址