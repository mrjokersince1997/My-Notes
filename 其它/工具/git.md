# Git 

---



## 基本概念

Git 是用来对代码进行版本管理和多人协作的代码仓库。

> Git 不区分文件名大小写，这在重命名文件时（仅更新文件名大小写）可能会造成隐患。

### 存储区域

![git](git.png)

- **Workspace 工作区** 
  
  当前开发者正在进行编写的代码版本。

- **Index 暂存区** 

  【目录树结构】记录要进行版本管理的文件名、文件状态信息以及指向文件的索引。

  通过使用时间戳、文件长度等状态信息，可以快速判断工作区文件是否被修改。

- **Repository 仓库区** 
  
  【日志结构】记录所有版本提交的历史更改，保存在 Git 对象库（.git/objects）中。

  通过读取仓库区，可以将工作区代码回退到任意版本。

- **Remote 远程仓库**

  【日志结构】将本地仓库保存在远程服务器中，如 Github/Gitlab/Gitee 等知名第三方代码托管网站。

  通过同步远程仓库，可以便捷地进行远程开发和多人协作。
  
---

## 初始配置


### 安装本地仓库

点击下方链接下载 Git ，即可通过 Git Bash 工具使用 Git 命令。

> 下载地址：https://git-scm.com/

*如果想要直接在控制台使用 git 命令，还需要配置环境变量：在 Path 中添加 Git/cmd 文件夹路径。*


### 链接远程仓库

如果想通过远程仓库托管代码，还需要将本地 Git 链接到第三方的远程仓库中，如 Github/Gitlab/Gitee 等知名第三方代码托管网站。
   
> Github 官方网址：https://github.com/


本地 Git 尝试对远程仓库修改时必须持有密钥，否则远程仓库将拒绝访问。

通过 Git Bash 命令生成本地密钥，然后添加到 Github 账号 SSH and GPG keys 选项中即可。

```bash
$ ssh-keygen -t rsa -b 4096 -C "email@example.com"        # 生成密钥
$ clip < ~/.ssh/id_rsa.pub                                # 将密钥复制到剪切板
```

---

## 创建项目

### 本地创建

如果要使用 Git 管理目录内的文件，需要将目录创建为 Git 代码仓库。
 
```bash
git init                             # 在现有目录创建 git 
git init myProject                   # 创建子目录并为子目录创建 git
```

为已创建的本地仓库链接远程仓库，每个远程仓库链接都有独立的标识。

```bash
git remote add origin git@github.com:account/project.git      # 绑定远程仓库，命名为 origin
git remote rm origin                                          # 移除远程仓库 origin
git remote -v                                                 # 查看绑定的远程仓库
```

### 远程克隆

远程仓库中的项目拥有唯一的标识 SSH: 如 `git@github.com:account/project.git` 。用户可以直接拷贝到本地。

```bash
git clone git@github.com:account/project.git                  # 拷贝项目到本地，并自动链接远程仓库
```


---

## 基本使用

### 暂存文件 add

需要进行版本管理的代码文件应首先放入暂存区。

```bash
git add README.md                          # 将指定文件放入暂存区
git add .                                  # 将全部文件放入暂存区

git diff                                   # 查看工作区更新（相对于暂存区）
git diff master                            # 查看工作区更新（相对于 master 分支）
git status                                 # 查看文件是否被暂存或提交

git checkout .                             # 放弃工作区修改（但不会删除新建文件）
git reset HEAD .                           # 放弃暂存区修改（但不会改变工作区）
```

在执行 add 操作时，开发者往往想要忽略一些特定的文件或目录。

我们可以通过在项目根目录下创建 `.gitignore` 文件记录忽略项的方式来实现，Git 在执行 add 操作时会自动跳过这些文件。
 
```bash
# .gitignore 文件

*.a                         # 忽略所有 .a 结尾的文件
!lib.a                      # 但 lib.a 除外

/TODO                       # 忽略项目根目录下的 TODO 文件

node_modules                # 忽略指定文件夹
.project
.vscode

build/                      # 忽略 build/ 目录下的所有文件
doc/*.txt                   # 忽略 doc/ 目录下的 txt 文件
```

尽管已更新了 `.gitignore` 文件，但暂存区仍可能保有历史文件的缓存数据。已提交过的文件如果想取消版本管理，要首先清除缓存。

```bash
git rm -r --cached .idea                   # 从暂存区删除文件夹
git rm -r --cached .                       # 从暂存区删除全部文件 
```

### 提交版本 commit

通过执行 commit 操作，将暂存区中托管的文件数据存储到本地仓库保存。

commit 操作执行完毕后，暂存区数据会被清空。每次 commit 操作前都应执行 add 操作。

```bash
git commit -m "Your commit"                # 提交文件，放入 git 仓库保存

git diff --staged                          # 查看当前分支暂存区更新（相对于提交版本）
git status                                 # 查看文件是否被暂存或提交
```

执行 commit 操作后（版本号 A）并尝试 push 到远程仓库，如果远程仓库已经被更新就会遭到拒绝。此时必须通过 pull 获取更新到本地然后合并（版本号 B）才能 push 代码，但是会提交两个版本更新。

此时可以改用 stash 操作对本地更改进行缓存，但不会产生新的提交对象（无论是否 add 默认情况下都会被缓存）。再执行 pull 操作时会直接读取远程仓库的版本，然后通过 stash pop 操作读取缓存并合并（版本号 B）。之后再进行 commit 和 push 操作，就只会提交一个版本更新。

```bash
git stash                                  # 将更改放入堆栈缓存
git stash save "save message"              # 将更改放入堆栈缓存并命名
  
git stash apply                            # 应用堆栈缓存中的更改但不清除
git stash pop                              # 读取堆栈缓存中的更改

git stash list                             # 查看堆栈缓存
git stash show "save message"              # 查看指定堆栈缓存
git stash clear                            # 清空堆栈缓存
```


### 管理分支 branch

在创建仓库的时候，会默认创建主分支 master。HEAD 则始终指向当前分支。

在开发功能、修复 BUG 时，我们通常都会创建分支来进行操作，直到完成开发后再合并到主分支上。

```bash
git branch                                 # 列出本地分支（HEAD 指向当前分支）
git branch -r                              # 列出远程分支 

git branch test                            # 创建 test 分支（但不切换）
git checkout test                          # 切换到 test 分支
git checkout -b test                       # 创建并切换到 test 分支


# 当前分支未被修改时，merge 和 rebase 无区别
git merge test                             # test 分支合并到当前分支（将两个分支合并，新建一个 commit）
git merge origin/master                    # origin/master 合并到当前分支   
git rebase master                          # master 分支合并到当前分支（当前分支重新执行另一个分支的全部 commit）

git branch -D test                         # 删除分支
```

当合并分支或者导入远程仓库分支时，常常会出现同一个文件被多个分支修改的情况。这个时候工作区文件会同时记录多个版本的代码，需要开发者通过编辑器解决冲突。

```bash
git status                                                        # 查看工作区和缓存区差异
git log -p master..origin/master                                  # 查看两分支提交版本之间差异

git log                                                           # 查看提交历史（会显示 commit ID）
git log README.txt                                                # 查看指定文件提交历史（会显示 commit ID）

git reset --hard f687a6de307a598d375bc1b6433dfe667c551f87         # 版本回退（根据 commit ID）
git reset f687a6de307a598d375bc1b6433dfe667c551f87 README.txt     # 对指定文件版本回退（根据 commit ID）
```


### 远程同步 push / pull

本地仓库和远程仓库链接后，且本地 Git 绑定的 GitHub 账户具备对远程仓库的操作权限，用户就可以通过以下指令同步远程代码。

```bash
git push -u origin master                                     # master 分支上传到远程仓库 origin（上传新分支会在远程仓库也创建新分支）
git push -f origin master                                     # master 分支强制上传到远程仓库 origin（适用于版本回退后远程同步）

git pull origin master                                        # 从远端仓库 origin 获取代码并自动合并到主分支
git status                                                    # 导入后工作区更新，查看和之前版本的差异

git fetch origin master                                       # 从远端仓库 origin 获取 origin/master 分支
git log -p master..origin/master                              # 查看分支差异
git merge origin/master                                       # 合并分支
```


### 合作开发 fork

Github 等远程仓库支持多人对同一项目进行协同开发，主要有以下两种形式。

- **collaborators 模式**

  适用于小组合作开发。

  1. 仓库所有者进入仓库设置，在 Collaborators 选项添加合作者。
   
  2. 合作者有权限对直接对指定的远程仓库进行修改。

- **fork 模式**

  适用于开源或大型项目。

  1. 开发者选择 Fork 原仓库，复制得到自己持有的镜像仓库。

  2. 开发者对镜像仓库进行修改后，可以发送 Pull Request 询问原仓库拥有者是否想要该修改。

  3. 原仓库拥有者同意后，镜像仓库的修改会合并到原仓库中。

> 协助开发者可以在本地仓库同时链接两个远程仓库：如 origin 自己持有的镜像仓库 & upstream 原始仓库。本地仓库从 upstream 获取代码，更新后上传到 origin 并发送 Pull Request 请求。


