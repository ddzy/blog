---
title: git常用命令集合
date: 2019-03-12 11:49:25
tags: [frontend, git]
categories: [github]
---

pr的时候遇到了分支合并的种种问题, 延申出了一些常用命令, 之前也有总结过, 今天抽空汇总一下.


<!-- more -->


## 更新

------

### [2019-3-16]

#### Added

- 新增`创建本地二层分支`命令
- 新增`git stash`储藏命令
- 新增`源仓库与fork仓库合并`命令

### [2019-3-24]

#### Added

- 新增`撤销merge`命令
- 新增`合并merge冲突`命令
- 新增`新建并关联origin分支`命令
- 新增`查看commit历史`命令

### [2019-3-30]

#### Added

- 新增`查看本地分支和远程分支的关联`

### [2019-4-11]

#### Added

- 新增`删除origin中的某个文件`

### [2019-4-12]

#### Added

- 新增`clone远程仓库的指定分支`

### [2019-5-14]

#### Added

- 新增`合并两个没有关联的分支`

### [2019-6-16]

#### Added

- 新增`git log`和`git reflog`的区别

### [2019-8-19]

#### Added

- 新增`查看本地分支的标签`
- 新增`查看远程分支的标签`
- 新增`给当前分支打标签, 并添加自定义信息`
- 新增`删除本地的某个标签`
- 新增`删除远程的某个标签`
- 新增`将本地的所有标签推送至远程`

## 记录

------

- 提交操作

```bash
git add .
git commit -m "xxx"
```

- 撤销某次push

```bash
git reset [id]
git pull origin master
git push origin master
```

- 回退到指定版本

```bash
git reset --hard [id]
git push -f
```

- 撤销某次add

```bash
git reset HEAD
```

- 撤销某次commit

```bash
git reset --hard [id]
git pull
```

- 查看远程仓库地址

```bash
git remote -v
```

- 查看提交记录

```bash
git log
```

- 查看指定版本

```bash
git checkout id
```

- 删除本地分支

```bash
git branch -D [xxx]
```

- 创建本地分支

```bash
git branch [xxx]
```

- 切换本地分支

```bash
git checkout [xxx]
```

- 推送当前所在分支到远程分支

```bash
git push origin [xxx]
```

- 查看本地分支

```bash
git branch
```

- 查看全部分支

```bash
git branch -a
```

- 查看远程分支地址

```bash
git remote -v
```

- 创建本地分支并推送到远程分支

```bash
git checkout -b [dev]
git pull origin [dev]
git push origin [dev]
```

- 创建远程分支

```bash
git checkout -b [本地分支名]
git push origin [本地分支名]:[远程分支名]
```

- 删除远程分支

```bash
git push origin :[远程分支名]
git push origin --delete [远程分支名]
```

- 创建本地二层分支

```bash
git checkout -b [new branch] [base branch]
```

- 暂存当前工作状态(切换分支之前使用)
  - git stash list
  - git stash save ""
  - git stash apply | pop
  - git stash drop stash@{index}

```bash
git stash save ""
git checkout [another branch]
git stash list
git stash pop
```

- 原仓库与fork仓库合并

```bash
# 同步原仓库与本地仓库
git remote -v
git remote add upstream [原仓库地址]
git checkout (master | branch/docs | xxx)
git fetch upstream (master | branch/docs | xxx)
git diff upstream/(master | branch/docs | xxx)
git merge upstream/(master | branch/docs | xxx)

# 同步本地仓库和fork仓库
git push origin (master | branch/docs | xxx) -f
```

- 撤销merge

```bash
git [reflog | log]
git reset --hard [id]
```

- 合并merge冲突

```bash
git fetch upstream [xxx]
git merge upstream [xxx]
# vscode手动合并冲突, 或者使用mergetool
git add .
git commit -m [xxx]
```

- 新建并关联origin分支

```bash
git checkout -b [xxx]
git push --set-upstream origin [xxx]
```

- 查看commit历史

```bash
# 当前分支
git log
# 所有分支
git reflog
```

- 查看本地分支和远程分支的关联

```bash
git branch -vv
```

- 删除origin中的某个文件

```bash
# 忘记加入.gitignore
# 已push到远程仓库的文件

git pull origin [xxx]
git rm -r --cached [x]
git commit -m
git push origin [xxx]
```

- clone远程仓库的指定分支

```bash
git clone -b [xxx] [remote address]
```

- 合并两个没有关联的分支

```bash
# 解决`fatal: refusing to merge unrelated histories`的错误

git merge --no-ff [another branch] --allow-unrelated-histories
```

- `git log`和`git reflog`的区别

```bash
# git log只查看当前分支的commit历史
git log

# git reflog查看当前项目的所有分支的提交历史
git reflog
```

- 查看本地分支的标签

```bash
git tag
```

- 查看远程分支的标签

```bash
git ls-remote --tag
```

- 给当前分支打标签, 并添加自定义信息

```bash
git tag v1.0.0 -m "release v1.0.0"
```

- 删除本地的某个标签

```bash
git tag -d v1.0.0
```

- 删除远程的某个标签

```bash
git push origin -d v1.0.0
```

- 将本地的所有标签推送至远程

```bash
git push origin --tag
```