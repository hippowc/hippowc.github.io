1、HEAD是什么？
git中每提交一次，会产生一个commitId，是一个hash值
HEAD代表的是当前版本，也就是最新一次提交的commitId，上一个版本是HEAD^，上上个版本是HEAD^^，上100个版本HEAD~
其实HEAD只是一个指针，指向当前版本。在使用reset命令进行版本回退或者前进（可以前进，只要有commitId），只是HEAD指针变化同时更新工作区。

2、git对象

git是一套内容寻址文件系统。意思是：git会简单存储键值对。可以插入任何内容（变更文件的快照），返回一个键值，然后通过该键值可以取出内容。
git的底层命令hash-object，这个命令可以将任何数据保存到.git/objects目录下，并返回键值。
如：$ echo 'test content' | git hash-object -w --stdin
可以通过cat-file命令取回该值
如：git cat-file -p d670460b4b4aece5915caf5c68d12f560a9fe3e4

git commit就相当于：
$ echo 'version 1' > test.txt
$ git hash-object -w test.txt
将最新的文件新增到文件库中。

git reset就相当于：
$ git cat-file -p 83baae61804e65cc73a7201a7252750c76066a30 > test.txt
将文件取出并覆盖到源文件

--blob对象，存储变更文件快照
--tree对象。tree中保存了仓库下的目录情况
git所有对象以tree或blob对象存储，tree相当于目录，blob相当于文件内容。
git的write-tree命令可以根据文件的index创建tree对象，当将文件放入暂存区中时，会自动创建index文件。
--commit对象，记录顶层树对象，作者信息，时间戳，注释信息。提交之后commit中保存的是顶级的tree目录的信息。

常用命令：
查看日志：
git log --pretty=oneline：查看提交记录，reset后会看不到之前的提交记录
git reflog：查看所有操作记录
恢复操作：
git checkout .  或 git checkout -- filename// 将工作区未add的恢复到原来状态
git stash  // 将未提交的修改暂存到stash。并清除工作区，git stash pop恢复
git reset HEAD . 或 git reset HEAD filename  // 撤销已经add的文件
git reset --hard HASH // 恢复到某个提交点，不保留修改  版本号不需要写全，足够查找就可以了。
git reset --soft HASH // 恢复到某特提交的，保留修改
比较命令：
git diff  // 比较工作区和暂存区
git diff HEAD // 比较工作区和本地仓库
git diff --cached  // 比较暂存区和本地仓库
提交命令：
git commit -am "comments" //可以直接提交

查看本地分支与远程分支对应关系：

git branch -vv

git remote show origin

cat .git/config
