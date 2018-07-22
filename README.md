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
git revert commitid 使用revert回退到指定的提交
git push 






二、Git删除

三、Git合并

四、Git别名