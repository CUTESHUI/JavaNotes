## Git

#### Git 历史

同生活中的许多伟大事物一样，Git 诞生于一个极富纷争大举创新的年代

Linux 内核开源项目有着为数众广的参与者。绝大多数的 Linux 内核维护工作都花在了提交补丁和保存归档的繁琐事务上(1991－2002年间)。到 2002 年，整个项目组开始启用一个专有的分布式版本控制系统 BitKeeper 来管理和维护代码

Linux社区中存在很多的大佬！破解研究 BitKeeper ！

到了 2005 年，开发 BitKeeper 的商业公司同 Linux 内核开源社区的合作关系结束，他们收回了 Linux 内核社区免费使用 BitKeeper 的权力。这就迫使 Linux 开源社区(特别是 Linux 的缔造者 Linus Torvalds)基于使用 BitKeeper 时的经验教训，开发出自己的版本系统。（2周左右！） 也就是后来的 Git！

**Git是目前世界上最先进的分布式版本控制系统。**

Git是免费、开源的，最初Git是为辅助 Linux 内核开发的，来替代 BitKeeper！



#### 常用Linux命令

- cd：改变目录
- cd . . ：回退到上一个目录，直接cd进入默认目录
- pwd：显示当前所在的目录路径
- ls(ll)：都是列出当前目录中的所有文件，只不过ll(两个ll)列出的内容更为详细
- touch : 新建一个文件 如 touch index.js 就会在当前目录下新建一个index.js文件
- rm：删除一个文件, rm index.js 就会把index.js文件删除
- mkdir：新建一个目录，就是新建一个文件夹
- rm -r：删除一个文件夹，rm -r src 删除src目录
- mv：移动文件
- reset：重新初始化终端/清屏
- clear：清屏
- history：查看命令历史
- help：帮助
- exit：退出
- #：表示注释



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



#### Git 项目搭建

- 本地仓库搭建

  - 创建全新的仓库，需要用 Git 管理的项目的根目录执行

  ```bash
  # 在当前目录新建一个Git代码库
  $ git init
  ```

  - 执行后可以看到，仅仅在项目目录多出了一个.git目录，关于版本等的所有信息都在这个目录里面。

- 克隆远程仓库

  - 将远程服务器上的仓库完全镜像一份至本地

  ```bash
  # 克隆一个项目和它的整个代码历史(版本信息)
  $ git clone [url]  # https://gitee.com/kuangstudy/openclass.git
  ```



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
#查看指定文件状态
git status [filename]

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



#### Git 分支

- 常用命令

```bash
# 列出所有本地分支
git branch

# 列出所有远程分支
git branch -r

# 新建一个分支，但依然停留在当前分支
git branch [branch-name]

# 新建一个分支，并切换到该分支
git checkout -b [branch]

# 合并指定分支到当前分支
$ git merge [branch]

# 删除分支
$ git branch -d [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```

- 如果同一个文件在合并分支时都被修改了则会引起冲突：解决的办法是我们可以修改冲突文件后重新提交。选择要保留他的代码还是你的代码
- master主分支应该非常稳定，用来发布新版本，一般情况下不允许在上面工作，工作一般情况下在新建的dev分支上工作，工作完后，比如上要发布，或者说dev分支代码稳定后可以合并到主分支master上来