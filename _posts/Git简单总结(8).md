git总结

## Git介绍  
> Git是一个分布式的版本控制系统,自动记录文件每个版本的变动信息  
Git由linux创始人Linus本人所创立，初衷是用来管理世界各地的linux开发者贡献的源代码
> * 集中式版本控制系统：版本库集中存放在中央服务器，必须联网才能工作。
> * 分布式版本控制系统：没有中央服务器，每台电脑都是一个完整的版本库，不必联网。但是在实际使用的时候，通常也有一台“中央服务器”，不过通常是为了方便交换数据。  

## Git常用命令
1.配置用户名和Email地址，由于Git是分布式的版本控制系统，所以每个机器必须有标识。
```
git config --global user.name "name"
git config --global user.email "email"
````
2.创建git可管理的仓库,运行一下命令之后当前目录下会多出一个.git文件用来追踪管理版本库
```
git init
```
>值得注意的是，所有的版本控制系统都只能追踪文本文件的改动。MS的word文件是二进制格式，因此没有办法被版本控制所管理。

3.添加文件到git仓库分为两步
```
git add <file>
git commit -m "commit information"
```
4.查看仓库当前的状态以及查看文件前后修改具体内容
```
git status
git diff <file>
```
5.每一个commit在Git里都会保存为一个快照。用HEAD表示当前版本，HEAD^表示上个版本，HEAD~100表示往上100个版本。版本回退与前进，只需要让Git内部的git指针指向想要的commit id即可
```
git log  //查看版本提交历史
git reset --hard HEAD^ //回退到上一个版本
git reset --hard <commit id> //commit id不需要写全，只需要写出前几个即可。
git reflog  //记录每一次git命令，常用来查询commit id
```
6.工作区和暂存区的概念。
> * 工作区一般指代当前目录 
> * .git目录一般叫做Git的版本库，其中最重要的是称为stage的暂存区还有一个Git自动创建的第一个分支master，指向master的指针就叫HEAD
> * git add把文件添加到暂存区
> * git commit把暂存区的内容提交到当前分支  

7.Git跟踪并管理的是修改而非文件，git只会提交添加到暂存区的修改。  
8.丢弃工作区的修改,以及把暂存区的修改回退到工作区
```
git checkout -- <file>
git reset HEAD <file>
```
9.删除文件，在git中删除也是修改。在工作区中删除了文件后，如果确实要删除则
```
git rm <file>
git commit -m "commit info"
```
如果删错了，就用git checkout -- ‘file’ ，实际上这条命令就是用版本库里的版本替换工作区的版本 

10.远程仓库，github就是git里的“中央管理器”，把一个已有的本地仓库与远程仓库关联
```
git remote add origin git@github.com:username/repository
git push -u origin master //第一次推送带上-u参数把本地master分支和远程master分支关联起来
git push origin master //后面推送可以省略-u参数
git clone git@github.com:username/repository //从远程仓库克隆
```
## Git分支管理
1.创建或查看分支
```
git branch //查看当前分支
git branch dev //创建dev分支  
```
2.切换分支
```
git checkout -b dev //创建并切换到dev分支
git checkout dev //切换到dev分支
git switch -c dev //创建并切换分支
git switch dev //切换分支
git merge <name> //合并指定分支到当前分支
git branch -d <name> //删除分支
```
3.git 多个分支提交存在冲突时，必须手动解决冲突再合并分支  
4.在手头工作没有完成且需要修复bug时，先git stash储存现场，然后修改bug，最后git stash pop 回到工作现场。git stash lish 可以查看stash  
5.丢弃一个没有被合并过的分支
```
git branch -D <name>
```
6.git remote查看远程库的信息 ，git pull从远程库抓取分支  
7.标签管理
```
git tag <name> //打标签，默认为HEAH，也可以在后面指定一个commit id
git tag //查看所有标签
git tag -d <name> //删除标签
```


