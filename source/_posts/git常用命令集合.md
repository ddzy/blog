---
title: git常用命令集合
date: 2019-03-12 11:49:25
tags: [frontend, git]
categories: [github]
---

pr 的时候遇到了分支合并的种种问题, 延申出了一些常用命令, 之前也有总结过, 今天抽空汇总一下.

<!-- more -->

## 更新

---

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

### [2019-12-13]

#### Added

- 新增 `删除origin仓库的深层文件夹`
- 新增 `git push -f`强制推送到 Gitlab 出错

### [2019-12-19]

#### Added

- 新增 `Fatal: out of memory, malloc failed (tried to allocate 1514190 bytes)`
- 新增 `Fatal: another git process seems to be running in this repository`

### [2019-12-20]

#### Added

- 新增命令: `重命名本地分支的标签`

#### Changed

- [更新](#更新) 区块新增区块折叠, 避免篇幅过长

### [2020-2-20]

#### Added

- 新增命令: `git打印文件夹结构`

### [2020-3-2]

#### Changed

- 更新命令: `git打印文件夹结构`

### [2020-4-14]

#### Added

- 新增命令: `强制覆盖本地分支`

### [2020-12-25]

#### Changed

- 更新命令: `git 打印文件夹结构`

### [2021-1-2]

#### Added

- 新增命令: `git pull 自动化拉取子模块`

### 【2021-1-13]

#### Changed

- 优化文章格式，`li` 列表下的子元素统一缩进处理，增强可读性

### [2021-1-14]

#### Added

- 新增命令：`更新远程分支`

### [2021-1-27]

#### Added

- 新增命令：`合并本地commit记录`

### [2021-2-23]

#### Added

- 新增命令：`取消某个本地分支与远程分支的关联`

## [2021-2-25]

#### Added

- 新增命令：`拉取一个具有子模块的项目的大致流程`
- 新增命令：`拉取远程的所有分支到本地的大致流程`

## [2021-3-17]

#### Added

- 新增命令：`合并分支，产生新的提交并保留`

## [2021-3-18]

#### Added

- 新增命令：`更改最近一次commit的用户名和邮箱`

## 记录

---

- 提交操作

  ```bash
  git add .
  git commit -m "xxx"
  ```

- 撤销某次 push

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

- 撤销某次 add

  ```bash
  git reset HEAD
  ```

- 撤销某次 commit

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

- 原仓库与 fork 仓库合并

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

- 撤销 merge

  ```bash
  git [reflog | log]
  git reset --hard [id]
  ```

- 合并 merge 冲突

  ```bash
  git fetch upstream [xxx]
  git merge upstream [xxx]
  # vscode手动合并冲突, 或者使用mergetool
  git add .
  git commit -m [xxx]
  ```

- 新建并关联 origin 分支

  ```bash
  git checkout -b [xxx]
  git push --set-upstream origin [xxx]
  ```

- 查看 commit 历史

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

- 删除 origin 中的某个文件

  ```bash
  # 忘记加入.gitignore
  # 已push到远程仓库的文件

  git pull origin [xxx]
  git rm -r --cached [x]
  git commit -m
  git push origin [xxx]
  ```

- clone 远程仓库的指定分支

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

- 重命名本地分支的标签

  ```bash
  git tag oldTagName newTagName
  git tag -d oldTagName
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

- 删除 `origin` 仓库的深层文件夹

  原本使用比较熟悉的 `git rm -r --cached xxx` 来删除, 但是发现并不能删除文件, 报 `fatal: pathspec 'xxx/xxx' did not match any files` 的错误, 也就是说远程的文件夹不存在, 这不可能啊??? 于是改用另一个命令:

  ```bash
  git filter-branch --force --index-filter 'git rm -r --cached --ignore-unmatch xxx/xxx' --prune -empty --tag-name-filter cat -- --all
  ```

  但是随即出现了 `fatal: bad revision 'rm'` 错误, 原因是 Windows 系统下要使用双引号, 将其改为:

  ```bash
  git filter-branch --force --index-filter "git rm -r --cached --ignore-unmatch xxx/xxx" --prune -empty --tag-name-filter cat -- --all
  ```

- `git push -f` 强制推送到 `Gitlab` 出错

  使用 `git filter-branch --force ...` 命令删除 origin 仓库的无用文件夹之后, 想强制推送并覆盖 Gitlab 的远程分支, 出现了 `remote: GitLab: You are not allowed to force push code to a protected branch on this project` 的错误.

  **原因是**: master 分支被保护了, 处于 `protected` 状态.

  **解决办法是**: 接触保护, 并将其置为 `unprotected` 状态.

- **Fatal: out of memory, malloc failed (tried to allocate 1514190 bytes)**

  **问题背景**: 想将本地的文章推送到 github 仓库, 执行完 `hexo g -d` 命令之后, 出现了这样的错误.

  **产生原因**: 文件大小超出了 git 传输的最大限制.

  **解决办法**: 既然超出了, 那就调大一些呗, 使用 `git config http.postbuffer 88888888` 来设置即可.

- **Fatal: another git process seems to be running in this repository**

  **问题背景**:

  VS Code 突然崩溃, 打开新的 git 进程准备推送代码的时候出现这个问题

  **解决办法**:

  将 `.git` 或者 `.deploygit` 文件夹下的 `index.lock` 文件删除即可

- git 打印文件夹结构

  ```
  # 安装依赖项
  https://www.cnblogs.com/elian/p/10083317.html

  # 对照各个参数即可
  tree --help

  # 推荐使用
  $ tree -L 2 -I "node_modules" -o ./result.txt --dirsfirst
  # 排除多个文件夹
  $ tree -L 2 -I "node_modules|dist|xxx" -o ./result.txt --dirsfirst
  ```

- 强制覆盖本地分支

  问题背景: 使用 `jenkins` 作自动化部署时, 本地回退了一次 `commit`, `jenkins` 构建报错, 提示 `git pull` 失败.

  解决办法: 自动化部署的时候, 可以直接使用远程分支覆盖本地分支:

  ```bash
  git fetch --all
  # 强制覆盖
  git reset --hard origin/master
  git pull
  ```

- `git pull` 自动化拉取子模块

  ```bash
  git pull --recurse-submodules
  ```

- 更新远程分支

  ```bash
  git remote update
  ```

- 合并本地commit记录

  ```bash
  # 参考：https://zhuanlan.zhihu.com/p/87318673
  # 假设有三个commit
  # 这是 commit3
  # 这是 commit2
  # 这是 commit1 => HEAD

  # 需求是将 commit3 合并到 commit2

  git rebase -i <commit1-id>

  # pick => 使用提交
  # reword => 使用提交，但修改该提交的说明
  # edit => 使用提交，进入 shell 以便进行提交修补
  # squash => 使用提交，但合并到上一个提交
  # fixup => 与 squash 类似，但会丢弃 commit message
  # drop => 删除提交
  # reset => 将 HEAD 指向到该标记
  # merge => 创建一个合并提交，并使用原始的 commit message（如果没有指定原始的 commit message，使用注释部分的 oneline 作为 commit message），使用 -c <提交> 可以编辑提交说明
  ```

- 取消某个本地分支与远程分支的关联

  ```bash
  git branch --unset-upstream
  ```

- 拉取一个具有子模块的项目的大致流程

  ```bash
  git clone https://github.com/ddzy/blog.git

  cd blog/themes/Nlvi

  git submodule update --init --recursive

  cd ../..

  npm install

  [npm cache clean --force]
  ```

- 拉取远程的所有分支到本地的大致流程
  ```bash
  # 默认情况下，只能 clone 单个分支
  git clone https://github.com/ddzy/blog.git

  # 可以通过 `-b` 来拉取指定分支
  git clone -b dev https://github.com/ddzy/blog.git

  # git 不能一次性拉取所有分支
  # origin 分支（dev、release、fixbug）
  # 第一步：默认处于 master 分支
  git checkout -b dev origin/dev
  # 第二步：此时处于 dev 分支
  git checkout -b release origin/release
  # 第三步：此时处于 fixbug 分支
  git checkout -b fixbug origin/fixbug

  # 实际上，处于哪个分支无所谓，只需要遵循如下规范就可以了
  git checkout -b xxx origin/xxx
  ```

- 合并分支，产生新的提交并保留
  ```bash
  # 比如有两个分支：dev 和 master
  # 当发布新版本的时候，需要将 dev 合并到 master，可以直接使用 `git merge`
  git merge dev

  # 但是假如我并不想在 master 上看到 dev 的历史 commit 记录，可以使用 `git merge --squash` 来产生一次新的提交
  git merge --squash dev
  git add .
  git commit -m "release: 发布新版本"
  ```
- 更改最近一次commit的用户名和邮箱
  ```bash
  git commit --amend --author='duanzhaoyang<duanzhaoyang@96966.com>'
  ```