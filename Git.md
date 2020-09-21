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

##### 2.4.2、git add [file-name]

将文件加入暂存区

- `file-name`：文件名称

##### 2.4.3、git reset HEAD [file-name]

将暂存区的文件取消暂存，取消暂存后，文件会回到暂存前的状态，如果之前是未跟踪，则回到未跟踪状态，其它状态同理

- `file-name`：文件名称

##### 2.4.4、git commit -m [message]

将暂存区的文件修改提交到本地仓库，如果想不每次先操作 `git add [filename]` 再 `commmit`，可以使用 `git commit -a -m [message]` 直接跳过保存至缓存区直接提交

- `message`：更新信息/日志信息

##### 2.4.5、git rm [file-name]

将文件删除并将此次操作加入暂存区

- `file-name`：文件名称

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

##### 2.4.8、git checkout -- [file-name]

撤销对文件的修改，请务必记得 `git checkout -- [file-name]` 是一个危险的命令。 你对那个文件在本地的任何修改都会消失——Git 会用最近提交的版本覆盖掉它。 除非你确实清楚不想要对那个文件的本地修改了，否则请不要使用这个命令

- `file-name`：文件名称

##### 2.4.9、git checkout [file-commit-id]

切换到当前分支的某个历史版本，可以先用 `git log` 查看日志确认版本的 `commit-id`，再使用 `git checkout [file-commit-id]` 进行切换

- `file-commit-id`：文件提交 `id`

##### 2.4.10、git commit --amend

修正最近一次 `commit` 操作，不需要自己重新添加 `message`，会使用最近一次 `commit` 的 `message`

#### 2.5、远程仓库操作

##### 2.5.1、git remote

查看远程仓库，列出指定的每一个远程服务器的简写，如果已经克隆了该仓库，那么至少应该能看到 `origin`，这是 `Git` 克隆的仓库服务器的默认名字

查看更多信息：

- `git remote -v`：查看关联的远程仓库 `fetch` 和 `push`
- `git remote show origin`：查看关联远程仓库的详细信息

##### 2.5.2、git remote add [remote-name] [url]

添加远程仓库

- `remote-name`：仓库名称，可随意填写，通常为 `origin`
- `url`：远程仓库 `url` 地址，需要填写一个已创建的远程仓库 `url` 地址

##### 2.5.3、git clone [url]

克隆远程仓库，默认配置下远程 `Git` 仓库中的每一个文件的每一个版本都将被拉取下来

- `url`：远程仓库 `url` 地址，需要填写一个已创建的远程仓库 `url` 地址

##### 2.5.4、git remote rm [remote-name]

移除本地仓库与远程仓库的关联

- `remote-name`：仓库名称，默认为 `origin`

##### 2.5.5、git fetch 与 git pull

> git fetch

`git fetch` 是从远程仓库获取最新版本到本地仓库，不会自动 `merge`

> git pull

`git pull` 也是从远程仓库获取最新版本到本地仓库，会自动 `merge`，注意：如果当前本地仓库不是从远程仓库克隆的，而是本地创建的仓库，并且仓库中存在文件，此时再从远程仓库拉取文件的时候会报错（`fatal: refusing to merge unrelated histories`），解决此问题可以在 `git pull` 命令后加入参数 `--allow-unrelated-histories`

##### 2.5.6、git push [remote-name] [branch-name]

推送到远程仓库，将本地仓库 `commit` 后的代码推送到远程仓库中，同步两个仓库中的文件

- `remote-name`：仓库名称，默认为 `origin`
- `branch-name`：分支名称，默认为 `master`

#### 2.6、分支操作

几乎所有的版本控制系统都以某种形式支持分支，使用分支意味着你可以把你的工作从主线开发上分离开来，以免影响开发主线，`Git` 的 `master` 分支并不是一个特殊分支，它跟其他分支没有区别，之所以每个仓库都有一个 `master` 分支，是因为 `git init` 默认创建它，并且大多数人都懒得去改动它

##### 2.6.1、git branch

查看分支

- `git branch`：列出所有本地分支
- `git branch -r`：列出所有远程分支
- `git branch -a`：列出所有本地分支和远程分支

##### 2.6.2、git branch [branch-name]

创建分支

- `branch-name`：分支名称

##### 2.6.3、git checkout [branch-name]

切换分支

- `branch-name`：分支名称

##### 2.6.4、git push [remote-name] [branch-name]

推送到远程仓库分支

- `remote-name`：仓库名称
- `branch-name`：分支名称

##### 2.6.5、git merge [branch-name]

合并分支，有时候合并操作不会如此顺利，如果你在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，`Git` 就没有办法合并它们，同时会提示文件冲突，此时需要我们打开冲突的文件并修复冲突内容，最后执行 `git add` 命令来标识冲突已解决

- `branch-name`：分支名称

##### 2.6.6、git branch -d [branch-name]

删除分支，如果要删除的分支中进行了一些开发操作，此时上面的命令并不会删除分支，如果坚持要删除分支，可以将命令改为 `git branch -D [branch-name]`

- `branch-name`：分支名称	

#### 2.7、标签操作

`Git` 可以给历史中的某一个提交打上标签，以示重要，比较有代表性的是人们会使用这个功能来标记发布节点（`v1.0`、`v1.2`等），标签指的是某个分支某个特定时间点的状态，通过标签，可以很方便的切换到标记时的状态

##### 2.7.1、git tag

列出所有标签，如果想查看某个 `tag` 信息，可以使用 `git show [tag-name]`

##### 2.7.2、git tag [tag-name]

创建一个标签

- `tag-name`：标签名称

##### 2.7.3、git push [remote-name] [tag-name]

将标签推送至远程仓库

- `tag-name`：标签名称

##### 2.7.4、git checkout -b [branch-name] [tag-name]

检出标签，需要先新建一个分支，然后指向某个 `tag`

- `branch-name`：新的分支名称
- `tag-name`：已存在的标签名称

##### 2.7.5、git tag -d [tag-name]  

删除本地标签

- `tag-name`：标签名称

##### 2.7.6、git push origin :refs/tags/[tag-name]

删除远程标签

- `tag-name`：标签名称



