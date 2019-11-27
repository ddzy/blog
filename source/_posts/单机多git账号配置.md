---
title: 单机多git账号配置
date: 2019-11-26 15:50:13
tags: [git, frontend, backend]
categories: [freebie]
---

由于需要在公司的电脑上登录不同的 Git 服务器:

- 公司的 Gitlab
- 自己的 Github

两者使用不同的邮箱, 所以需要在一台电脑上配置不同的 Git, 特此记录完整的配置过程.


<!-- more -->


## 更新

------

### [2019-11-26]

- Initial release

## 环境

------

- win10@1903
- vscode@1.40.1
- gitbash

## 步骤

------

### 配置 SSH

1. 进入 `C` 盘下的 `.ssh` 文件夹

```bash
cd ~/.ssh
```

2. 创建 `Git` 配置文件

```bash
vim ./config
```

3. 写入 `config` 相关配置

```bash
# git 服务器 1

# 服务器名称
# 和 HostName 保持一致, 不然会报错
Host git.3k.com
# 域名
HostName git.3k.com
# 用户名(随意)
User duanzhaoyang
# SSH 密钥, 文件地址可自定义
IdentityFile ~/.ssh/gitlab_3k/id_rsa_gitlab_3k

# git 服务器 2

Host github.com
HostName github.com
User ddzy
IdentityFile ~/.ssh/github/id_rsa_github
```

4. 生成 `SSH Key`

> 文件的名称和 `config` 文件中一致

```bash
ssh-keygen -C "xxx@xx.com" -t rsa
```

将生成的文件按照名称 **剪切** 到对应的目录下, 最终的目录结构是这样的:

```plain
~/.ssh
  gitlab_3k
    id_rsa_gitlab_3k
    id_rsa_gitlab_3k.pub
  github
    id_rsa_github
    id_rsa_github.pub
  config
  ...
```

5. Github | Gitlab 配置 SSH

> Github 和 Gitlab 的做法大同小异, 所以只记录 Github.

进入 [https://github.com/settings/keys](https://github.com/settings/keys), 创建一个 `SSH Key`, 将 `~/.ssh/github/id_rsa_github.pub` 公钥复制过去.

### 测试 SSH

上面已经配置好了 Github 的 SSH, 接着测试一下:

```bash
ssh -T git@github.com
```

如果出现:

```plain
Hi ddzy! You've successfully authenticated, but Github does not provide shell access...
```

则表明 `SSH` 配置成功.

### 配置项目 Git

进入到自己的 `Git` 项目, 以我目前的项目(`ddzy.github.io`)为例, 执行命令:

```bash
git config user.name xxx(和 config 文件中的一致)
git config user.email xxx@xx.com
```

接着打开项目下的 `.git/config` 文件, 可以看到:

```bash
[core]
  ...
[remote "origin"]
  url = https://github.com/ddzy/ddzy.github.io
  fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
  remote = origin
  merge = refs/heads/master
[user]
  name = xxx
  email = xxx@xx.com
```

接着修改上述配置的 `url` 项:

- Host 为对应的 config 配置文件中的 Host
- User 对应 config 中的 User

```diff
- url = https://github.com/ddzy/ddzy.github.io
+ url = git@Host:User/ddzy.github.io.git
```

### 测似项目 Git

在当前项目下执行 `git push`, 或者其它有效命令即可.

## 错误捕捉

------

### ssh: Could not resolve hostname xxx: Name or service not known

> 产生原因

`~/.ssh/config` 中的 `Host` 和 `HostName` 不相等.

> 解决办法

将两者设为相同的地址即可.

## 参考

------

- [Git配置多账号登录不同项目](https://www.imooc.com/article/7419)
- [github设置添加SSH](https://www.cnblogs.com/ayseeing/p/3572582.html)