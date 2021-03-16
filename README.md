# atom

每次提交，git会将提交串成一条时间线（分支）
HEAD不是指向提交，而是指向当前分支，当前分支才是指向提交的

开始，master为一条线，git用master指向最新的提交，再用HEAD指向master
这样就能确定当前分支及当前分支的提交点。每次提交，master分支都会向前移动一步

创建新的分支，Git新建一个指针指向master相同的提交，再把HEAD指向新建分支，表示当前分支位置
所以Git合并分支也很快，就改改指针，工作区内容也不变
合并完成甚至可以删除dev分支

创建分支：
git checkout -b branch_name  命令加上-b参数表示创建并切换，相当于   
git branch branch_name   
git checkout branch_name   
然后使用 git branch 查看分支信息，在当前分支前会有个* 号

合并及删除分支   
git merge dev ，改名了用于合并指定分支到当前分支，Fast-forward为快进模式，直接把master指向dev的当前提交
git branch -d dev 删除分支，-D强制删除分支

最新版的Git提供新的命令：   
git switch -c dev 创建并切换到新的dev分支   
git switch master 直接切换到已有的master分支 Git会自动提示当前分支比远程分支超前x个提交

Git用 <<<<<<<, =======, >>>>>>>标记处不同分支的内容

合并分支时，如果可能Git会用Fast forward模式（删除分支后，会丢掉分支信息）
强制禁用 Fast forward， Git会在merge时生成一个新的commit，从而在分支历史上课看出分支信息

--no-ff 方式的git merge：  
$ git merge --no-ff -m "merge with no-ff" dev

因为本次合并要创建一个新的commit， 所以加上-m参数把commit描述写进去   
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，
而fast forward合并就看不出来曾经做过合并。

bug修复分支要从稳定主线最新提交处签出

Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：git stash
用git status查看工作区，就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复bug
Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法
一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除；
另一种方式是用git stash pop，恢复的同时把stash内容也删了：

bug修改，将修改的提交所做的修改复制到dev分支，ps： 只想复制修复这个提交所做的更改，并不是把整个master分支merge过来
为了方便操作，Git专门提供了一个cherry-pick命令，让我们能复制一个特定的提交到当前分支：
在master分支上修复的bug，想要合并到当前dev分支，可以用git cherry-pick <commit>命令，把bug提交的修改“复制”到当前分支，避免重复劳动。

提交历史分叉了如何解决？
这是在dev开发的hotfix
rebase 做了什么操作呢？
首先， git 会把 feature1 分支里面的每个 commit 取消掉；
其次，把上面的操作临时保存成 patch 文件，存在 .git/rebase 目录下；
然后，把 feature1 分支更新到最新的 master 分支；
最后，把上面保存的 patch 文件应用到 feature1 分支上；

commit记录，feture1分支是基于hotfix合并后的master，自然而然称为了最领先的分支，而且无merge的commit记录

在 rebase 的过程中，也许会出现冲突 conflict 。在这种情况， git 会停止 rebase 并会让你去解决冲突。在解决完冲突后，用 git add 命令去更新这些内容。
注意，你无需执行 git-commit，只要执行 continue
git rebase --continue
这样 git 会继续应用余下的 patch 补丁文件。

7.在任何时候，我们都可以用 --abort 参数来终止 rebase 的行动，并且分支会回到 rebase 开始前的状态。
git rebase —abort

git rebase工作原理：   
`(dev分支)git rebase mater`   
1. git会将dev中的每个commit取消掉，会将操作临时保存成patch文件保存在.git/rebase 目录下
2. 再将dev分支更新到最新的master分支
3. 最后将保存的patch文件应用到dev分支上

ps: 在rebase过程中，也会出现冲突conflict。此时git会停止rebase并会让你去解决冲突，解决完后使用git add命令去更改这些内容。

在解决rebase conflict时，你无需执行git commit， 只要进行continue `git rebase --continue`
这样git会继续应用余下的patch补丁文件

任何时候，都可以使用--abort参数来终止rebase的行动，并且分支会回到rebase开始前的状态   
`git rebase --abort`



### iOS沙盒机制  
iOS中每个应用都放在了统一的文件夹目录下，每个应用都有其独立的存储空间, 应用只能在为该程序创建的文件系统中读写文件。沙盒相当于每个应用程序的系统目录，与应用程序一一对应，其名称为随机分配。沙盒机制采用一种类似于Lisp的配置语言来描述那些资源系统可以访问，哪些禁止访问。这些资源包括文件、系统服务、网络连接、内存资源等。每个沙盒之间互相独立，应用程序不能访问非本程序的沙盒，可以说沙盒机制让iOS系统变得更安全。  

* **每个应用程序都有自己的存储空间**  
* **应用程序不能翻过自己的围墙去访问别的存储空间的内容**
* **应用程序请求的数据都要通过权限检测，假如不符合条件的话，不会被放行**

> APP之间不能相互通,唯独可以通过URL Scheme可以通信；  
用于存放如图像、 声音、 映像、偏好设置和文本文件等非代码文件；  
每次项目杀死后重新运行对应的沙盒路径是永不相同的。

#### 根目录
使用如下方式获取App根目录：   
`NSString *homeDirectory = NSHomeDirectory();`  
结构如下：  

.
├── Documents
├── Library
│   ├── Caches
│   └── Preferences
├── SystemData
└── tmp  

