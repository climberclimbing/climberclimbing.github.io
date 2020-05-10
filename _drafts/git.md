## 创建本地仓库

### 创建一个本地仓库

git init

### 为该仓库配置用户信息，在提交代码时将使用该用户信息进行提交

git config user.name "climberclimbing"

git config user.email "allen3715@sina.com"

注：此处用户信息仅对当前仓库有效，如果要配置全局用户信息需添加--global选项：

git config --global user.name "用户"

git config --global user.email "邮箱"

### 为该仓库添加忽略文件配置

touch .gitignore

编辑该文件

### 将所有文件添加到暂存区

git add -A

### 查看暂存区的状态

git status

### 将暂存区代码提交到仓库

git commit -m "create a repository"

查看commit日志

git log

## Git对仓库的常用操作

### 文件的跟踪、修改、提交

touch readme.txt

git add readme.txt

git status

git commit -m "创建readme.txt"

将已跟踪的文件跳过暂存区提交(-a 参数)

git commit -a -m"创建并提交了readme.txt"

git log

### 手动删除工作区文件(即直接删除目录下文件) 或者 用 git rm "文件名"

git status

文件找回前提：删除文件前，文件已经提交到了本地库。

文件找回方法：

1. git log 找到离没删文件前最近的commit id(哈希值)；

2. 将操作过的其它文件转移；

3. git reset --hard "commit id"；

### 文件比较

先修改文件

git status

git diff (HEAD)

### 操作的撤销

用暂存区中filename文件来覆盖工作区中的filename文件

git checkout -- readme.txt # 撤销之前对 readme.txt 的操作

git status

拉取最近一次提交到版本库的文件到暂存区 并且该操作不影响工作区

git reset HEAD README.md  # HEAD指最新的commit的哈希值 也可以指定哈希值

简单的来说 就是可以帮我们从版本库中 拉取文件到 暂存区 当我们把工作区的某个文件弄乱了 我们就可以使用该命令 把版本库中的那个文件拉到暂存区 然后在拉回工作区(git checkout -- readme.txt)。

### 给指定的某个 commit id 加tag

git tag -a v0.01 4247ffe2844d2c2a9ada9b4c3ee5a2d36875acb7 -m "给提交的版本打标签"

### 推送当前分支的标签到远程服务器

git tag

git push origin v0.01

### 检出标签代码(需要有远程仓库)

git checkout -b version0 v0.01

注：该命令创建了一个新的分支version0，并获取了v0.01tag的代码

## git分支

### 分支的基本操作

### 查看分支

git branch 

### 分支的创建

git branch newBranch

### 删除分支

git branch -D ewBranch

### 分支切换

git checkout newBranch

### 将本地分支上传到远程服务器

git push -u origin version0

### 远程分支的基本操作

### 查看远程仓库的信息

git remote

### 添加新的远程仓库

git remote add URL

### 克隆一个远程仓库

git clone URL

### 将数据(commit)提交到远程仓库

git push origin

### 从远程仓库拉取更新

git fetch

注：使用git fetch后，并不会将新的内容更新到工作区域的文件中，所以可以通过git diff master origin/master命令来比较差异：


注：使用git fetch后，并不会将新的内容更新到工作区域的文件中，所以可以通过git diff master origin/master命令来比较差异：

### 将更新合并到工作区域 

git merge

注：git pull命令相当于执行了git fetch和git merge两个命令。

##



    "Pull Request 是一种通知机制。你修改了他人的代码，将你的修改通知原来的作者，希望他合并你的修改，这就是 Pull Request。"

Pull Request 本质上是一种软件的合作方式，是将涉及不同功能的代码，纳入主干的一种流程。这个过程中，还可以进行讨论、审核和修改代码。




