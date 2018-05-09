---
title: git代码回滚
date: 2018-05-03 18:30:53
categories: 版本控制
tags:
  - git
---
#### 场景1
> 糟了，我刚把不想要的代码，commit到本地仓库中了，但是还没有做push操作！

- 文件修改未执行git add操作
```
$git checkout fileName
$git checkout .
```

- 同时对多个文件执行了git add操作，但本次只想提交其中一部分文件
```
$ git add *
$ git status
# 取消暂存
$ git reset HEAD <filename>
```

- 文件执行了git add操作，但想撤销对其的修改
```
# 取消暂存
git reset HEAD fileName
# 撤销修改
git checkout fileName
```

- 修改的文件已被git commit，但想再次修改不再产生新的Commit
```
# 修改最后一次提交
$ git add sample.txt
$ git commit --amend -m"说明"
```

- 已在本地进行了多次git commit操作，现在想撤销到其中某次Commit
```
git reset [--hard|soft|mixed|merge|keep] [commit|HEAD]
```

#### 场景2
> 彻底完了，刚线上更新的代码出现问题了，需要还原这次提交的代码！

**注意：**
已进行git push，即已推送到“远程仓库”中。我们将已被提交到“远程仓库”的代码还原操作叫做“回滚”！注意：对远程仓库做回滚操作是有风险的，需提前做好备份和通知其他团队成员！

- 通过tag发布
```
$git checkout <tag>
```

- 回到当前的分支
```
$git checkout <branch_name>
```

- 撤销指定文件到指定版本
```
# 查看指定文件的历史版本
git log <filename>
# 回滚到指定commitID
git checkout <commitID> <filename>
```

- 删除最后一次远程提交
```
# 方式一：使用revert
$git revert HEAD
$git push origin master

# 方式二：使用reset
$git reset --hard HEAD^
$git push origin master -f
```

**二者区别：**
revert是放弃指定提交的修改，但是会生成一次新的提交，需要填写提交注释，以前的历史记录都在；
reset是指将HEAD指针指到指定提交，历史记录中不会出现放弃的提交记录。

