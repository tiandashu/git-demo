Git 基础

### Git配置ssh

1. git config --global user.name "tiandashu"
   git config --global user.email "test@qq.com"

2. ssh key-gen -t rsa -C "test@qq.com"
   三次回车不用设置密码得到2个文件在C:\Users\Administrator\.ssh目录下

3. cat ~/.ssh/id_rsa.pub
   获取公钥

4. 登录Github -> setting -> ssh添加完成

### Git的配置文件

1. git init 在项目的根目录初始化

2. .gitignore 文件里添加不需要Git托管的文件，正则匹配
   eg: node_modules   .idea


### Git的基本理念

暂存区(git开始托管资源) -> 本地仓库() -> 远程仓库()

### Git操作

一、Git回滚
```
git reset [commit] 
git reset有三个参数soft、mixed、hard分别对应head的指针移动、index（暂存区）、以及工作目录的修改，当缺省时，默认为mixed参数。

git revert [commit]
git revert与reset的区别是git revert会生成一个新的提交来撤销某次提交，此次提交之前的commit都会被保留，也就是说对于项目的版本历史来说是往前走的。而git reset 则是回到某次提交，类似于穿越时空。
```

现在先假设几个环境，本文将会给出相应的解决方法： 
0. 当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file
git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

1. 本地代码（或文件）已经add但是还未commit
2. 要回退的commit的代码已经commit了，但是还未push到远程个人repository 
这两种情况下均可以使用下面的操作
git reset --hard  工作区的已经add的文件会被删除毛都不剩，未被add的文件不受git管理不会删除。慎用
但是现在想找回删除的文件：两种方法
```
方法1：
git fsck --lost-found 并不会直接找回
打开.git/lost-found/other这个路径下的文件
将这些文件拷贝到一个其他的地方（毕竟小心得来的残骸，就要长点心好好保护啦！）
将这些文件命令成原来的名字就可以恢复了，很麻烦

方法2：
git reflog 查看操作记录
git reset --hard 上一次的commitid   
```

3. 要回退的commit的代码已经push到远程的个人分支，但是还未merge到公共的repository

步骤一： git reset commitid     本地先会退到指定的提交，此时远程仓库的代码还是最新的没有回退
步骤二： git push -f  origin master     将本地回退的强制更新到远程仓库。注意：本地分支回滚后，版本将落后远程分支，必须使用强制推送覆盖远程分支，否则无法推送到远程分支


4. 要回退的commit的代码已被merge（合入)到公共的repository

git revert commitid 使用revert回退到指定的提交,但是会生成一次提交记录，版本整体是向前的。这样的好处是不会回退掉别人提交的代码
git push 

二、Git分支

git checkout -b newbranchName   在本地生成一个新分支并切换过去
git checkout -b dev origin/dev  在本地创建远程的dev分支
git push --set-upstream origin newbranchName  建立本地分支和远程分支的关联
注意：虽然我们是从master创建的分支，但是在新分支上改动的提交不能直接push到master，需要在远程创建一个同步分支

三、Git删除

删除本地分支： git branch -d branchName
强制删除本地分支： git branch -D branchName    //如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除。
删除远程分支： git push origin --delete BranchName

四、Git合并
```
git Merge 
合并某分支到当前分支：git merge <name>
这种合并是将两个分支的历史合并到一起，现有的分支并不会被更改,它会比对双方不同的文件缓存下来，生成一个commit,去push

优点: 安全，现有分支不会被修改
缺点: 或多或少都会污染一点分支历史，在回看项目时会增加理解项目历史的难度
用处: 一般用于公共master主分支
通常，合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
git merge --no-ff -m "merge with no-ff" dev  需要添加-m参数因为会形成一次新的commit提交
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。
![--no-ff]('./0.png')



git Rebase
这种合并通常称之为“衍合”，他是修改提交历史，比对双方的commit，然后找出不同的去缓存，然后在去push，修改你的commit历史。

优点: 项目历史会非常整洁

缺点: 安全性和可跟踪性很差，你将无法知晓你这次合并做了那些修改

用处: 绝不要在公共的分支上使用它。一般用于，自己本身独自使用的分支

总结
这两种方式各有优点和缺点，我们要根据实际情况和需要去决定去使用哪种合并方式。我的使用习惯一般是: 在我自己持有使用的分支，使用Rebase，保持好看的项目历史，在主master分支时使用Merge，这样安全和好跟踪修改！
```

五、Git别名
git config --global alias.s status
git config --global alias.b branch

六、远程分支

git remote add origin  git@github.com:tiandashu/demo.git
git remote add origin  git@github.com:tiandashu1/demo.git
为本地的git仓库添加远程git仓库，可以同时添加多个仓库且远程仓库的名字origin可以随便命名

git push -u origin master 第一次向远程提交的时候加上-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

git push -f origin master 添加-f是强制更新远程仓库


七、 Git stash

情景：在dev开发一个功能开发到一半了，但是需要紧急修复master上的bug
直接切换分支肯定是不行的，但是dev开发到一半了直接commit也是不行的。可以先用git stash把当前工作现场“储藏”起来，等以后恢复现场后继续工作
```
git stash
git checkout master     //master上的bug先切回master
git checkout -b bug001  //master创建bug分支，在这上面修改bug
git add .
git commit -m 'bug修改完成了'
git checkout master     //切回master准备合并
git merge --no-ff -m "merged bug fix 101" bug001


git checkout dev   //在dev接着干活
git status         //nothing to commit, working tree clean
//工作区是干净的，刚才的工作现场存到哪去了？用git stash list命令看看
git stash list     //工作现场被保存了
git stash pop      //恢复现场
git stash list     //没有信息了
```

八、Git tag

Git的tag总是和commitid挂钩，是为了更方便的找到某次提交的commitid

```
//切换到需要打标签的分支上
git checkout master
git tag v1.0
git tag   //查看所有标签

默认标签是打在最新提交的commit上的。有时候，如果忘了打标签，比如，现在已经是周五了，但应该在周一打的标签没有打，怎么办？
方法是找到历史提交的commit id，然后打上就可以了：
git log --pretty=oneline --abbrev-commit
git tag v0.9 f52c633
git tag  //查看所有标签

//注意，标签不是按时间顺序列出，而是按字母排序的。可以用git show <tagname>查看标签信息：
git show v0.9

//如果标签打错了，也可以删除：
git tag -d v0.1

//因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。
//如果要推送某个标签到远程，使用命令
git push origin <tagname>

//如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：
git tag -d v0.9
git push origin :refs/tags/v0.9
```