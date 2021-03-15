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
