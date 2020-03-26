# git问题处理
1. 项目一不小心坏掉了，如何处理
```shell
git reflog
```
2. 列出你在git上所有记录的索引，你只要找到HEAD@{index}前面所有对应的操作索引，并使用下面命令即可
```shell
git reset HEAD@{index}
```
3. 想改个小东西，但代码不小心提交了(commit)了
```shell
git add .
```
 	然后运行下面这条命令，它就会把刚刚添加的代码合并到最后一次提交上了
```shell
git commit --amend
```
4. 刚刚历史写的不够好需要重新写
```shell
git commit --amend
```
8. 我刚刚不小心把新分支的代码提交到主分支上了！ 我们一步步来，你先创建个新分支（some-new-branch-name）：

```shell
git branch some-new-branch-name
```

 	然后把刚才的提交从主分支中移除：
```shell
git reset HEAD~ --hard
```
​	需要注意的是，上面的代码只会切换到最后一条提交记录上,如果你已跑到其它提交记录上怎	么办？
​	没关系，你可以用 git reset HEAD@{number} 再跑回来
​	等你跑回来之后，我们再切换到新分支上:

```shell
git checkout some-new-branch-name
```
​	需要注意的是，上面的代码只对本地仓库有效，
​	如果你已经把代码提交到远程仓库上，
​	那就得跟队友商量下解决方案了。

9. 完蛋了，我把代码提交到错误的分支上了 //我们先撤销最后一次提交，但保留变更代码：


```shell
git reset HEAD~ --soft 
git stash 
```

 	再切到你想要提交的正确分支（name-of-the-correct-branch）上， 并把变更代码提交上去：

```shell
git checkout name-of-the-correct-branch 
git stash pop $ git add . 
git commit -m "your message here"
```

 	OK，到这里就搞定了。
	如果想要逼格高点，也可以用 cherry-pick 这个命令来完成上面那些操作。
	具体的操作步骤如下。 
	首先，切换到正确的分支上：

```shell
git checkout name-of-the-correct-branch
```

​	然后使用 cherry-pick 来获取最新一条提交记录：
```shell
git cherry-pick master
```
​	最后再把主分支上那条提交错误的记录删除：
```shell
git checkout master 
git reset HEAD~ —-hard
```

10. 咦？为啥我运行 diff 后啥都没有？ 遇到这种情况，应该是文件没有加入到暂存区的缘故。 解决方案很简单， 咱们要么把文件加入到暂存区，要么就直接使用下面这条命令： 

```shell
git diff --staged 
```

    这样，就可以看到未存入暂存区文件的 diff 效果啦。

11. 如果本地代码仓库把自己折腾得乱七八糟，不用怕，用下面这招，一击必杀：
```shell
cd .. 
sudo rm -r fucking-git-repo-dir 
git clone https://some.github.url/fucking-git-repo-dir.git 
cd fucking-git-repo-dir 
```
​	是的，这就是备胎（线上仓库）的强大之处，只要你备胎尚在，你就可以大大方方的把本地仓库删了，clone 备胎，从	头再来。