## Git

#### Git 历史

同生活中的许多伟大事物一样，Git 诞生于一个极富纷争大举创新的年代

Linux 内核开源项目有着为数众广的参与者。绝大多数的 Linux 内核维护工作都花在了提交补丁和保存归档的繁琐事务上(1991－2002年间)。到 2002 年，整个项目组开始启用一个专有的分布式版本控制系统 BitKeeper 来管理和维护代码

Linux社区中存在很多的大佬！破解研究 BitKeeper ！

到了 2005 年，开发 BitKeeper 的商业公司同 Linux 内核开源社区的合作关系结束，他们收回了 Linux 内核社区免费使用 BitKeeper 的权力。这就迫使 Linux 开源社区(特别是 Linux 的缔造者 Linus Torvalds)基于使用 BitKeeper 时的经验教训，开发出自己的版本系统。（2周左右！） 也就是后来的 Git！

**Git是目前世界上最先进的分布式版本控制系统。**

Git是免费、开源的，最初Git是为辅助 Linux 内核开发的，来替代 BitKeeper！



#### Git 基本理论

- Git本地有三个工作区域
  - 工作目录 / Working Directory
    - 平时存放项目代码的地方
  - 暂存区 / Stage / Index
    - 用于临时存放你的改动，事实上它只是一个文件，保存即将提交到文件列表信息
  - 资源库 / Repository / Git Directory
    - 安全存放数据的位置，这里面有你提交到所有版本的数据。其中HEAD指向最新放入仓库的版本
  - 如果在加上远程的 git仓库 / Remote Directory 就可以分为四个工作区域
    - 托管代码的服务器，可以简单的认为是你项目组中的一台电脑用于远程数据交换

- 工作流程
  - 在工作目录中添加、修改文件
  - 将需要进行版本管理的文件放入暂存区域
  - 将暂存区域的文件提交到git仓库。
  - 因此，Git 管理的文件有三种状态：已修改（modified），已暂存（staged），已提交(committed)



#### Git 文件操作

- 文件状态
  - Untracked
    - 未跟踪，此文件在文件夹中，但并没有加入到git库，不参与版本控制
    - 通过 git add 变为 Staged 状态
  - Unmodify
    - 文件已经入库未修改，版本库中的文件快照内容与文件夹中完全一致
    - 这种类型的文件有两种去处
    - 如果被修改，变为 Modified
    - 执行 git rm，移出版本库，变成 Untracked 
  - Modified
    - 文件已修改，仅仅是修改，没有进行其他的操作
    - 这种类型的文件有两种去处
    - 执行 git add，变成 Staged 
    - 执行 git checkout，放弃修改，变成 Unmodify
  - Staged
    - 暂存状态
    - 执行 git commit，将修改同步到库中，这时库中的文件和本地文件又变为一致，变成 Unmodify
    - 执行 git reset HEAD filename，取消暂存，变成 Modified
- 查看文件状态

```bash
#查看指定文件(xxx)状态
git status 

#查看所有文件状态
git status
```

- 忽略文件

  - 有些时候我们不想把某些文件纳入版本控制中，比如数据库文件，临时文件，设计文件等
  - 在主目录下建立".gitignore"文件，此文件有如下规则
    - 忽略文件中的空行或以井号（#）开始的行将会被忽略
    - 可以使用Linux通配符。例如：星号（*）代表任意多个字符，问号（？）代表一个字符，方括号（[abc]）代表可选字符范围，大括号（{string1,string2,...}）代表可选的字符串等
    - 如果名称的最前面有一个感叹号（!），表示例外规则，将不被忽略
    - 如果名称的最前面是一个路径分隔符（/），表示要忽略的文件在此目录下，而子目录中的文件不忽略。
    - 如果名称的最后面是一个路径分隔符（/），表示要忽略的是此目录下该名称的子目录，而非文件（默认文件或目录都忽略）

  ```bash
  #为注释
  *.txt        #忽略所有 .txt结尾的文件,这样的话上传就不会被选中！
  !lib.txt     #但lib.txt除外
  /temp        #仅忽略项目根目录下的TODO文件,不包括其它目录temp
  build/       #忽略build/目录下的所有文件
  doc/*.txt    #会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
  ```

---

#### Git 项目搭建

- 本地仓库搭建

  ```bash
  # 在当前目录新建一个Git代码库
  $ git init
  ```

- 克隆远程仓库

  ```bash
  # 克隆一个项目和它的整个代码历史(版本信息)
  $ git clone [url]  # https://gitee.com/kuangstudy/openclass.git
  ```

- 在工作目录中添加、修改文件

- 将需要进行版本管理的文件放入暂存区域

  ```bash
  # 将当前目录下所有的文件放入
  $ git add . 
  
  # 将当前目录下制定的文件(xxx)放入
  $ git add xxx
  ```

- 将暂存区域的文件提交到 Git仓库

  ```bash
  # xxx对提交的描述
  $ git commit -m ‘xxx’
  ```

- 将远程仓库的READE.md拉到本地仓库

  ```bash
  $ git pull --rebase origin master
  ```

- 连接远程仓库

  ```bash
  $ git remote add origin '远程仓库url'
  ```

- 提交到远程仓库

  ```bash
  # 第一次需要这样
  $ git push -u origin master
  
  # 之后
  $ git push
  ```




## Git 常用命令

#### 仓库

```bash
# 在当前目录新建一个Git代码库
$ git init

# 新建一个目录，将其初始化为Git代码库
$ git init [project-name]

# 下载一个项目和它的整个代码历史
$ git clone [url]
```



#### 配置

```bash
# 显示当前的Git配置
$ git config --list

# 编辑Git配置文件
$ git config -e [--global]

# 设置提交代码时的用户信息
$ git config [--global] user.name "[name]"
$ git config [--global] user.email "[email address]"
```



#### 查看信息

```bash
# 显示有变更的文件
$ git status

# 显示当前分支的版本历史
$ git log

# 显示commit历史，以及每次commit发生变更的文件
$ git log --stat

# 搜索提交历史，根据关键词
$ git log -S [keyword]

# 显示某个commit之后的所有变动，每个commit占据一行
$ git log [tag] HEAD --pretty=format:%s

# 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
$ git log [tag] HEAD --grep feature

# 显示某个文件的版本历史，包括文件改名
$ git log --follow [file]
$ git whatchanged [file]

# 显示指定文件相关的每一次diff
$ git log -p [file]

# 显示过去5次提交
$ git log -5 --pretty --oneline

# 显示所有提交过的用户，按提交次数排序
$ git shortlog -sn

# 显示指定文件是什么人在什么时间修改过
$ git blame [file]

# 显示暂存区和工作区的差异
$ git diff

# 显示暂存区和上一个commit的差异
$ git diff --cached [file]

# 显示工作区与当前分支最新commit之间的差异
$ git diff HEAD

# 显示两次提交之间的差异
$ git diff [first-branch]...[second-branch]

# 显示今天你写了多少行代码
$ git diff --shortstat "@{0 day ago}"

# 显示某次提交的元数据和内容变化
$ git show [commit]

# 显示某次提交发生变化的文件
$ git show --name-only [commit]

# 显示某次提交时，某个文件的内容
$ git show [commit]:[filename]

# 显示当前分支的最近几次提交
$ git reflog
```



#### 增加/删除文件

```bash
# 添加指定文件到暂存区
$ git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
$ git add [dir]

# 添加当前目录的所有文件到暂存区
$ git add .

# 添加每个变化前，都会要求确认
# 对于同一个文件的多处变化，可以实现分次提交
$ git add -p

# 删除工作区文件，并且将这次删除放入暂存区
$ git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]

# 改名文件，并且将这个改名放入暂存区
$ git mv [file-original] [file-renamed]
```



#### 代码提交

```bash
# 提交暂存区到仓库区
$ git commit -m [message]

# 提交暂存区的指定文件到仓库区
$ git commit [file1] [file2] ... -m [message]

# 提交工作区自上次commit之后的变化，直接到仓库区
$ git commit -a

# 提交时显示所有diff信息
$ git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
$ git commit --amend -m [message]

# 重做上一次commit，并包括指定文件的新变化
$ git commit --amend [file1] [file2] ...
```



#### 分支

```bash
# 列出所有本地分支
$ git branch

# 列出所有远程分支
$ git branch -r

# 列出所有本地分支和远程分支
$ git branch -a

# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

# 新建一个分支，并切换到该分支
$ git checkout -b [branch]

# 新建一个分支，指向指定commit
$ git branch [branch] [commit]

# 新建一个分支，与指定的远程分支建立追踪关系
$ git branch --track [branch] [remote-branch]

# 切换到指定分支，并更新工作区
$ git checkout [branch-name]

# 切换到上一个分支
$ git checkout -

# 建立追踪关系，在现有分支与指定的远程分支之间
$ git branch --set-upstream [branch] [remote-branch]

# 合并指定分支到当前分支
$ git merge [branch]

# 选择一个commit，合并进当前分支
$ git cherry-pick [commit]

# 删除分支
$ git branch -d [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```



#### 标签

```bash
# 列出所有tag
$ git tag

# 新建一个tag在当前commit
$ git tag [tag]

# 新建一个tag在指定commit
$ git tag [tag] [commit]

# 删除本地tag
$ git tag -d [tag]

# 删除远程tag
$ git push origin :refs/tags/[tagName]

# 查看tag信息
$ git show [tag]

# 提交指定tag
$ git push [remote] [tag]

# 提交所有tag
$ git push [remote] --tags

# 新建一个分支，指向某个tag
$ git checkout -b [branch] [tag]
```



#### 远程同步

```bash
# 下载远程仓库的所有变动
$ git fetch [remote]

# 显示所有远程仓库
$ git remote -v

# 显示某个远程仓库的信息
$ git remote show [remote]

# 增加一个新的远程仓库，并命名
$ git remote add [shortname] [url]

# 取回远程仓库的变化，并与本地分支合并
$ git pull [remote] [branch]

# 上传本地指定分支到远程仓库
$ git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突
$ git push [remote] --force

# 推送所有分支到远程仓库
$ git push [remote] --all
```



#### 撤销

```bash
# 恢复暂存区的指定文件到工作区
$ git checkout [file]

# 恢复某个commit的指定文件到暂存区和工作区
$ git checkout [commit] [file]

# 恢复暂存区的所有文件到工作区
$ git checkout .

# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
$ git reset [file]

# 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
$ git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
$ git reset --hard [commit]

# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
$ git reset --keep [commit]

# 新建一个commit，用来撤销指定commit
# 后者的所有变化都将被前者抵消，并且应用到当前分支
$ git revert [commit]

# 暂时将未提交的变化移除，稍后再移入
$ git stash
$ git stash pop
```



#### 其他

```bash
# 生成一个可供发布的压缩包
$ git archive
```



