
# **Git常用命令及方法大全**

![](Git命令_001.png)

下面是我整理的常用 Git 命令清单。几个专用名词的译名如下。

* Workspace：工作区
* Index / Stage：暂存区
* Repository：仓库区（或本地仓库）
* Remote：远程仓库

## **本地分支关联远程**

```
git branch --set-upstream-to=origin/分支名 分支名
```
## 代码库修改密码后push不上去怎么办？

```
1. // 重新输入密码
2. git config --system --unset credential.helper
3.
4. // 密码存储同步
5. git config --global credential.helper store
```

## 一、新建代码库

```
1.
2. # 在当前目录新建一个Git代码库
3. $ git init
4.
5. # 新建一个目录，将其初始化为Git代码库
6. $ git init [project-name]
7.
8. # 下载一个项目和它的整个代码历史
9. $ git clone [url]
```
## 二、配置

Git的设置文件为`.gitconfig`，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。

```
1.
2. # 显示当前的Git配置
3. $ git config --list
4.
5. # 编辑Git配置文件
6. $ git config -e [--global]
7.
8. # 设置提交代码时的用户信息
9. $ git config [--global] user.name "[name]"
10. $ git config [--global] user.email "[email address]"
```
## 三、增加/删除文件

```
1.
2. # 添加指定文件到暂存区
3. $ git add [file1] [file2] ...
4.
5. # 添加指定目录到暂存区，包括子目录
6. $ git add [dir]
7.
8. # 添加当前目录的所有文件到暂存区
9. $ git add .
10.
11. # 添加每个变化前，都会要求确认
12. # 对于同一个文件的多处变化，可以实现分次提交
13. $ git add -p
14.
15. # 删除工作区文件，并且将这次删除放入暂存区
16. $ git rm [file1] [file2] ...
17.
18. # 停止追踪指定文件，但该文件会保留在工作区
19. $ git rm --cached [file]
20.
21. # 改名文件，并且将这个改名放入暂存区
22. $ git mv [file-original] [file-renamed]
```
## 四、代码提交

```
1.
2. # 提交暂存区到仓库区，消息内容为版本信息
3. $ git commit -m [message]
4.
5. # 提交暂存区的指定文件到仓库区
6. $ git commit [file1] [file2] ... -m [message]
7.
8. # 提交工作区自上次commit之后的变化，直接到仓库区
9. $ git commit -a
10.
11. # 提交时显示所有diff信息
12. $ git commit -v
13.
14. # 使用一次新的commit，替代上一次提交
15. # 如果代码没有任何新变化，则用来改写上一次commit的提交信息
16. $ git commit --amend -m [message]
17.
18. # 重做上一次commit，并包括指定文件的新变化
19. $ git commit --amend [file1] [file2] ...
```
## 五、分支

```
1.
2. # 列出所有本地分支
3. $ git branch
4.
5. # 列出所有远程分支
6. $ git branch -r
7.
8. # 列出所有本地分支和远程分支
9. $ git branch -a

		# 将master分支移到main：
    git branch -m master main
10.
11. # 新建一个分支，但依然停留在当前分支
12. $ git branch [branch-name]
13.
14. # 以远程分支为基础新建一个分支，并切换到该分支
15. $ git checkout -b [branch] origin/[remote-branch]
16.
17. # 新建一个分支，指向指定commit
18. $ git branch [branch] [commit]
19.
20. # 新建一个分支，与指定的远程分支建立追踪关系
21. $ git branch --track [branch] [remote-branch]
22.
23. # 切换到指定分支，并更新工作区
24. $ git checkout [branch-name]
25.
26. # 切换到上一个分支
27. $ git checkout -
28.
29. # 建立追踪关系，在现有分支与指定的远程分支之间
30. $ git branch --set-upstream [branch] [remote-branch]
31.
32. # 合并指定分支到当前分支
33. $ git merge [branch]
34.
35. # 选择一个commit，合并进当前分支
36. $ git cherry-pick [commit]
37.
38. # 删除分支
39. $ git branch -d [branch-name]
40.
41. # 删除远程分支
42. $ git push origin --delete [branch-name]
43. $ git branch -dr [remote/branch]
```
## 六、标签

```
1.
2. # 列出所有tag
3. $ git tag
4.
5. # 新建一个tag在当前commit
6. $ git tag [tag]
7.
8. # 新建一个tag在指定commit
9. $ git tag [tag] [commit]
10.
11. # 删除本地tag
12. $ git tag -d [tag]
13.
14. # 删除远程tag
15. $ git push origin :refs/tags/[tagName]
16.
17. # 查看tag信息
18. $ git show [tag]
19.
20. # 提交指定tag
21. $ git push [remote] [tag]
22.
23. # 提交所有tag
24. $ git push [remote] --tags
25.
26. # 新建一个分支，指向某个tag
27. $ git checkout -b [branch] [tag]
```
## 七、查看信息

```
1.
2. # 显示有变更的文件
3. $ git status [-s]
4.
5. # 显示当前分支的版本历史（长信息为完整版本号）
6. $ git log
7.
8. # 显示commit历史，以及每次commit发生变更的文件
9. $ git log --stat
10.
11. # 搜索提交历史，根据关键词
12. $ git log -S [keyword]
13.
14. # 显示某个commit之后的所有变动，每个commit占据一行
15. $ git log [tag] HEAD --pretty=format:%s
16.
17. # 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
18. $ git log [tag] HEAD --grep feature
19.
20. # 显示某个文件的版本历史，包括文件改名
21. $ git log --follow [file]
22. $ git whatchanged [file]
23.
24. # 显示指定文件相关的每一次diff
25. $ git log -p [file]
26.
27. # 显示过去5次提交
28. $ git log -5 --pretty --oneline
29.
30. # 显示所有提交过的用户，按提交次数排序
31. $ git shortlog -sn
32.
		# 查看提交Tree
    $ gitk

33. # 显示指定文件是什么人在什么时间修改过
34. $ git blame [file]
35.
36. # 显示暂存区和工作区的差异
37. $ git diff

		# 比较两个分支的差异
    $ git diff master..test
    $ git diff master...test //两者公有的父分支 与 test分支之间的差异
38.
39. # 显示暂存区和上一个commit的差异
40. $ git diff --cached [file]
41.
42. # 显示工作区与当前分支最新commit之间的差异
43. $ git diff HEAD
44.
45. # 显示两次提交之间的差异
46. $ git diff [first-branch]...[second-branch]
47.
48. # 显示今天你写了多少行代码
49. $ git diff --shortstat "@{0 day ago}"
50.
51. # 显示某次提交的元数据和内容变化
52. $ git show [commit]
53.
54. # 显示某次提交发生变化的文件
55. $ git show --name-only [commit]
56.
57. # 显示某次提交时，某个文件的内容
58. $ git show [commit]:[filename]
59.
60. # 显示当前分支的最近几次提交（缩略版本号）
61. $ git reflog
```
## 八、远程同步

```
1.
2. # 下载远程仓库的所有变动
3. $ git fetch [remote]
4.
5. # 显示所有远程仓库
6. $ git remote -v
7.
8. # 显示某个远程仓库的信息
9. $ git remote show [remote]
10.
		# 链接到远程仓库
    git remote add origin <server>

11. # 增加一个新的远程仓库，并命名
12. $ git remote add [shortname] [url]
13.
14. # 取回远程仓库的变化，并与本地分支合并
15. $ git pull [remote] [branch]
16.
17. # 上传本地指定分支到远程仓库
18. $ git push [remote] [branch]

git push <remote 名字> <本地分支的名字> : <远程库的名字>
git push origin HEAD:refs/for/feature/multi-subscription
19. refs/for :意义在于我们提交代码到服务器之后是需要经过code review 之后才能进行merge的
refs/heads 不需要

20. # 强行推送当前分支到远程仓库，即使有冲突
21. $ git push [remote] --force
22.
23. # 推送所有分支到远程仓库
24. $ git push [remote] --all
```
## 九、撤销

```
1.
2. # 恢复暂存区的指定文件到工作区
3. $ git checkout [file]
4.
5. # 恢复某个commit的指定文件到暂存区和工作区
6. $ git checkout [commit] [file]
7.
8. # 恢复暂存区的所有文件到工作区
9. $ git checkout .
10.
11. # 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
12. $ git reset [file]

重置暂存区为1条commit以前一致
git reset HEAD~1 删除第一条

丢弃工作区的改动
git restore .
13.
14. # 重置暂存区与工作区，与上一次commit保持一致
15. $ git reset --hard
16.
17. # 重置当前分支的指针为指定commit(版本号)，同时重置暂存区，但工作区不变
18. $ git reset [commit]
19.
20. # 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit(版本号)一致
21. $ git reset --hard [commit]
22.
23. # 重置当前HEAD为指定commit，但保持暂存区和工作区不变
24. $ git reset --keep [commit]
25.
26. # 新建一个commit，用来撤销指定commit
27. # 后者的所有变化都将被前者抵消，并且应用到当前分支
28. $ git revert [commit]
29.
30. # 暂时将未提交的变化移除，稍后再移入（加入储藏队列）
31. $ git stash

# 添加stash并注释
$ git stash save "test1"

    # 查看队列
		$ git stash list
    	stash@{0}: WIP on book: 51bea1d... fixed images
			stash@{1}: WIP on master: 9705ae6... changed the browse code to the official repo

    # 恢复到之前的状态
    $ git stash apply

    # 使用任意一个储藏 但不会删除
    $ git stash apply stash@{1}

    # 使用栈顶的储藏 弹栈对应的会删除栈中的stash记录
32. $ git stash pop

# 从堆栈中删除莫格指定的stash
$ git stash drop + 名称

# 从堆栈中清除所有内容
$ git stash clear

# 查看堆栈中最新保存的stash和当前目录的差异
$ git stash show

git clean 参数

具体：

git clean -n     // 显示将要删除的文件和目录

git clean -f     // 删除文件

git clean -df    // 删除文件和目录
```
## 十、其他

```
1.
2. # 生成一个可供发布的压缩包
3. $ git archive
```
# Git分支管理策略

**一、主分支Master**

首先，代码库应该有一个、且仅有一个主分支。所有提供给用户使用的正式版本，都在这个主分支上发布。

![](Git命令_002.png)

Git主分支的名字，默认叫做Master。它是自动建立的，版本库初始化以后，默认就是在主分支在进行开发。

**二、开发分支Develop**

主分支只用来分布重大版本，日常开发应该在另一条分支上完成。我们把开发用的分支，叫做Develop。

![](Git命令_003.png)

这个分支可以用来生成代码的最新隔夜版本（nightly）。如果想正式对外发布，就在Master分支上，对Develop分支进行"合并"（merge）。

Git创建Develop分支的命令：

　　git checkout -b develop master

将Develop分支发布到Master分支的命令：

　　# 切换到Master分支

　　git checkout master

　　# 对Develop分支进行合并

　　git merge --no-ff develop

这里稍微解释一下，上一条命令的--no-ff参数是什么意思。默认情况下，Git执行"快进式合并"（fast-farward merge），会直接将Master分支指向Develop分支。

![](Git命令_004.png)

使用--no-ff参数后，会执行正常合并，在Master分支上生成一个新节点。为了保证版本演进的清晰，我们希望采用这种做法。关于合并的更多解释，请参考Benjamin Sandofsky的[《Understanding the Git Workflow》](http://sandofsky.com/blog/git-workflow.html)。

![](Git命令_005.png)

**三、临时性分支**

前面讲到版本库的两条主要分支：Master和Develop。前者用于正式发布，后者用于日常开发。其实，常设分支只需要这两条就够了，不需要其他了。

但是，除了常设分支以外，还有一些临时性分支，用于应对一些特定目的的版本开发。临时性分支主要有三种：

　　\* 功能（feature）分支

　　\* 预发布（release）分支

　　\* 修补bug（fixbug）分支

这三种分支都属于临时性需要，使用完以后，应该删除，使得代码库的常设分支始终只有Master和Develop。

**四、 功能分支**

接下来，一个个来看这三种"临时性分支"。

第一种是功能分支，它是为了开发某种特定功能，从Develop分支上面分出来的。开发完成后，要再并入Develop。

![](Git命令_006.png)

功能分支的名字，可以采用feature-\*的形式命名。

创建一个功能分支：

　　git checkout -b feature-x develop

开发完成后，将功能分支合并到develop分支：

　　git checkout develop

　　git merge --no-ff feature-x

删除feature分支：

　　git branch -d feature-x

**五、预发布分支**

第二种是预发布分支，它是指发布正式版本之前（即合并到Master分支之前），我们可能需要有一个预发布的版本进行测试。

预发布分支是从Develop分支上面分出来的，预发布结束以后，必须合并进Develop和Master分支。它的命名，可以采用release-\*的形式。

创建一个预发布分支：

　　git checkout -b release-1.2 develop

确认没有问题后，合并到master分支：

　　git checkout master

　　git merge --no-ff release-1.2

　　# 对合并生成的新节点，做一个标签

　　git tag -a 1.2

再合并到develop分支：

　　git checkout develop

　　git merge --no-ff release-1.2

最后，删除预发布分支：

　　git branch -d release-1.2

**六、修补bug分支**

最后一种是修补bug分支。软件正式发布以后，难免会出现bug。这时就需要创建一个分支，进行bug修补。

修补bug分支是从Master分支上面分出来的。修补结束以后，再合并进Master和Develop分支。它的命名，可以采用fixbug-\*的形式。

![](Git命令_007.png)

创建一个修补bug分支：

　　git checkout -b fixbug-0.1 master

修补结束后，合并到master分支：

　　git checkout master

　　git merge --no-ff fixbug-0.1

　　git tag -a 0.1.1

再合并到develop分支：

　　git checkout develop

　　git merge --no-ff fixbug-0.1

最后，删除"修补bug分支"：

　　git branch -d fixbug-0.1

# 版本回退-撤销文件修改

{针对文件修改恢复}

## 工作区修改一个文件后，又想回到修改前(git add前)

1. 当然可以直接手动再在工作区中将文件修改回去

2. 修改后，通过命令git status查看

```
1. $ git status
2. # On branch master
3. # Changes not staged for commit:
4. #   (use "git add <file>..." to update what will be committed)
5. #   (use "git checkout -- <file>..." to discard changes in working directory)
6. #
7. #       modified:   readme.txt
8. #
9. no changes added to commit (use "git add" and/or "git commit -a")
```
这时Git会告诉你，git checkout -- file可以丢弃工作区的修改：

```
$ git checkout -- readme.txt
```
**Note**:

1. git checkout -- file命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到git checkout命令。

2. 命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：

一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。总之，就是让这个文件回到最近一次git commit或git add时的状态。

3. 工作区、暂存区的概念不清楚的可见于[Git版本控制教程 - Git本地仓库](http://blog.csdn.net/pipisorry/article/details/44588351)

## 如果在工作区中修改了文件还git add到暂存区（但是在commit之前）

用git status查看一下，修改只是添加到了暂存区，还没有提交：

```
1. $ git status
2. # On branch master
3. # Changes to be committed:
4. #   (use "git reset HEAD <file>..." to unstage)
5. #
6. #       modified:   readme.txt
7. #
```
Git同样告诉我们，用命令git reset HEAD file可以把暂存区的修改撤销掉（unstage），重新放回工作区：

```
1. $ git reset HEAD readme.txt
2. Unstaged changes after reset:
3. M       readme.txt
```
git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。

再用git status查看一下，现在暂存区是干净的，工作区有修改。

然后丢弃工作区的修改

```
1. $ git checkout -- readme.txt
2.
3. $ git status
4. # On branch master
5. nothing to commit (working directory clean)
```
## 不但修改了文件还从暂存区提交commit到了版本库 - 版本回退

版本回退可以回退到上一个版本。不过，这是有条件的，就是你还没有把自己的本地版本库推送到远程。Git是分布式版本控制系统。

在工作中对某个文件（如readme.txt）进行多次修改交commit。

可以通过版本控制系统命令告诉我们提交的历史记录，在Git中，我们用git log命令查看：

```
1. $ git log
2. commit 3628164fb26d48395383f8f31179f24e0882e1e0
3. Author: Michael Liao <askxuefeng@gmail.com>
4. Date:   Tue Aug 20 15:11:49 2013 +0800
5.
6.     append GPL
7.
8. commit ea34578d5496d7dd233c827ed32a8cd576c5ee85
9. Author: Michael Liao <askxuefeng@gmail.com>
10. Date:   Tue Aug 20 14:53:12 2013 +0800
11.
12.     add distributed
13.
14. commit cb926e7ea50ad11b8f9e909c05226233bf755030
15. Author: Michael Liao <askxuefeng@gmail.com>
16. Date:   Mon Aug 19 17:51:55 2013 +0800
17.
18.     wrote a readme file
```
**Note**:

1. git log命令显示从最近到最远的提交日志，我们可以看到3次提交，最近的一次是append GPL，上一次是add distributed，最早的一次是wrote a readme file。

2. 如果嫌输出信息太多，看得眼花缭乱的，可以试试加上--pretty=oneline参数：

```
1. $ git log --pretty=oneline
2. 3628164fb26d48395383f8f31179f24e0882e1e0 append GPL
3. ea34578d5496d7dd233c827ed32a8cd576c5ee85 add distributed
4. cb926e7ea50ad11b8f9e909c05226233bf755030 wrote a readme file
```
3. 你看到的一大串类似3628164...882e1e0的是commit id（版本号），和SVN不一样，Git的commit id不是1，2，3……递增的数字，而是一个SHA1计算出来的一个非常大的数字，用十六进制表示，而且你看到的commit id和我的肯定不一样，以你自己的为准。为什么commit id需要用这么一大串数字表示呢？因为Git是分布式的版本控制系统，后面我们还要研究多人在同一个版本库里工作，如果大家都用1，2，3……作为版本号，那肯定就冲突了。

4. 每提交一个新版本，实际上Git就会把它们自动串成一条时间线。如果使用可视化工具（如GitX、github的客户端、pycharm）查看Git历史，就可以更清楚地看到提交历史的时间线。

### 现在我们想要把readme.txt回退到上一个版本

如“add distributed”的那个版本，怎么做呢？首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，也就是最新的提交3628164...882e1e0（注意我的提交ID和你的肯定不一样），上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。

现在，我们要把当前版本“append GPL”回退到上一个版本“add distributed”，就可以使用git reset命令：

```
1. $ git reset --hard HEAD^
2. HEAD is now at ea34578 add distributed
```
这时readme.txt的内容就成了版本add distributed

我们用git log再看看现在版本库的状态：

```
1. $ git log
2. commit ea34578d5496d7dd233c827ed32a8cd576c5ee85
3. Author: Michael Liao <askxuefeng@gmail.com>
4. Date:   Tue Aug 20 14:53:12 2013 +0800
5.
6.     add distributed
7.
8. commit cb926e7ea50ad11b8f9e909c05226233bf755030
9. Author: Michael Liao <askxuefeng@gmail.com>
10. Date:   Mon Aug 19 17:51:55 2013 +0800
11.
12.     wrote a readme file
```
最新的那个版本append GPL已经看不到了！

## 恢复文件后，要是我们又想回到修改后的文件呢？（命令行窗口还没有被关掉）

{这个是git reset --hard后，又反悔了，想回到修改后的状态}

只要上面的命令行窗口还没有被关掉，你就可以顺着往上找啊找啊，找到那个append GPL的commit id是3628164...，于是就可以指定回到未来的某个版本：

```
1. $ git reset --hard 3628164
2. HEAD is now at 3628164 append GPL
```
版本号没必要写全，前几位就可以了，Git会自动去找。

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的HEAD指针，当你回退版本的时候，Git仅仅是把HEAD从指向append GPL：

![](Git命令_008.jpg)

改为指向add distributed：

![](Git命令_009.jpg)

然后顺便把工作区的文件更新了。所以你让HEAD指向哪个版本号，你就把当前版本定位在哪。

## 恢复文件后，要是我们又想回到修改后的文件呢？（命令行窗口早就关掉了）

{这个是git reset --hard后，又反悔了，想回到修改后的状态}

想恢复到新版本怎么办？找不到新版本的commit id怎么办？当你用$ git reset --hard HEAD^回退到add distributed版本时，再想恢复到append GPL，就必须找到append GPL的commit id。

Git提供了一个命令git reflog用来记录你的每一次命令：[[Git高级教程](http://blog.csdn.net/pipisorry/article/details/50669350):git log与git reflog]

```
1. $ git reflog
2. ea34578 HEAD@{0}: reset: moving to HEAD^
3. 3628164 HEAD@{1}: commit: append GPL
4. ea34578 HEAD@{2}: commit: add distributed
5. cb926e7 HEAD@{3}: commit (initial): wrote a readme file
```
第二行显示append GPL的commit id是3628164，现在，你又可以乘坐时光机回到未来了。

