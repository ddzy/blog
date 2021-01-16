---
title: hexo多端同步配置
date: 2019-11-27 15:34:04
tags: [hexo, git]
categories: [freebie, frontend, backend]
---

想在公司的大屏 `PC` 上更新博客, 方便省事, 但是相关的 `Hexo` 源文件都在笔记本上, 所以就需要在两者之间同步配置文件, 故将过程记录下来.


<!-- more -->


## 更新

------

### [2019-11-27]

- Initial release

### [2019-12-1]

#### Changed

- 更新文章格式

### [2020-2-3]

#### Added

- 新增问题: `'themes/Nlvi' already exists in the index`

### [2020-11-25]

#### Changed

- 更新问题: `'themes/Nlvi' already exists in the index'` 的解决方式

### [2021-1-16]

#### Changed

- 优化文章缩进格式

## 说明

------

- laptop(`我的笔记本, hexo 源文件所在地`)
- pc(`公司电脑`)

## 步骤

------

### 上传 hexo 配置

> **先捋一下思路:** 将 `laptop` 的 `hexo` 源配置文件, 使用 `git` 无缝同步到 `pc` 上, 使得 `pc` 也可以更新博客.

- \[**laptop**]: 初始化 git

	```bash
	# 进入博客的根目录, 我的是 blog-site
	cd blog-site

	git init
	```

- \[**laptop**]: 创建新的 hexo 配置专用分支

	```bash
	# branch/hexo 为存储 hexo 配置文件的专用分支
	git checkout -b branch/hexo
	```

- \[**laptop**]: 编辑 .gitignore

	```diff
	.DS_Store
	Thumbs.db
	db.json
	*.log
	node_modules/
	public/
	.deploy*/
	-
	+ .git
	```

- \[**laptop**]: 上传 hexo 配置到远程分支

	```bash
	git push --set-upstream origin branch/hexo
	```

- \[**laptop**]: branch/hexo 分支目录结构

	```plain
	- scaffolds
	- source
	- themes
		- Nlvi
		- landscape
	- .gitignore
	- README.md
	- _config.yml
	- package-lock.json
	- package.json
	```

- \[**laptop**]: 将 branch/hexo 设为默认分支

	由于后续的拉取、推送等操作都在 `branch/hexo` 分支上进行, 而不是原本的 `master`, 所以将 `branch/hexo` 设为默认分支.

	为什么呢? 因为 `hexo deploy` 命令, 只是将 `public` 目录下的站点文件上传到 `master`.

	![设置默认分支](https://oos.blog.yyge.top/2019/11/27/hexo%E5%A4%9A%E7%AB%AF%E5%90%8C%E6%AD%A5%E9%85%8D%E7%BD%AE/images/11.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

### 下载 hexo 配置

- \[**pc**]: clone 分支

	```bash
	# 拉取 blog 仓库
	# 此时已经设置了默认分支为 branch/hexo, 而不是 master, 所以拉取的是 branch/hexo 下的内容
	git clone https://github.com/ddzy/blog.git
	```

- \[**pc**]: 安装相关依赖项

	```bash
	npm install -g hexo-cli
	npm install
	```

### 第一次测试

> 在 `pc` 上检验 hexo 配置是否运行正常.

```bash
hexo g -d
```

浏览器打开博客, 可以看到, 此时页面是空白的, 这是由于 `laptop` 的 `hexo` 源目录中 `theme` 目录下的各个主题都是单独的 `git` 文件夹, 因此在之前使用 `git push --set-upstream origin branch/hexo` 的时候, 会被忽略, 并没有将主题文件夹上传上去, 导致文件夹是空白的, 进而页面显示空白.

**解决方法** 是使用 `submodule` 来管理子 `git` 模块.

### git submodule 模块划分

- \[**laptop**]: 将主题加入 submodule

	```bash
	# 进入博客根目录下
	cd blog/

	# 注意: 要提前将喜欢的主题 fork 到自己仓库下, 便于后续的更改和提交
	# 将主题仓库下载至 blog/themes 目录下, 文件夹命名为 Nlvi
	git submodule add https://github.com/ddzy/hexo-theme-Nlvi themes/Nlvi
	```

- \[**laptop**]: 查看 .gitmodules 文件

	这是执行完 `git submodule add` 之后自动生成的一个文件, 保存了一种映射关系, 内容大致是这样的:

	```bash
	[submodule "themes\\nlvi"]
		path = themes\\nlvi
		url = https://github.com/ddzy/hexo-theme-Nlvi.git
	[submodule "themes/Nlvi"]
		path = themes/Nlvi
		url = https://github.com/ddzy/hexo-theme-Nlvi
	```

- \[**laptop**]: 更新 branch/hexo 仓库

	```bash
	git add .
	git commit -m ":construction: use submodule to manage the child git repo"
	git push origin branch/hexo
	```

	可以看到, 此时 `branch/hexo` 仓库的 `themes` 文件夹下是有内容的:

	![git submodule结果](https://oos.blog.yyge.top/2019/11/27/hexo%E5%A4%9A%E7%AB%AF%E5%90%8C%E6%AD%A5%E9%85%8D%E7%BD%AE/images/22.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

- \[**注意**]: 'themes/Nlvi' already exists in the index

	`git submodule add` 之后可能会出现上述错误, 这是由于指定的文件夹名称已存在, 所以需要干掉它:

	```bash
	git rm -r --cached themes/Nlvi
	```

	接着再添加子模块:

	```bash
	git submodule add https://github.com/ddzy/hexo-theme-Nlvi themes/Nlvi
	```

### 第二次测试

- \[**pc**]: 再次拉取 branch/hexo 分支

	```bash
	git fetch origin branch/hexo
	git diff
	git merge --no-ff origin/branch/hexo
	```

- \[**pc**]: 没有看到 themes/Nlvi 主题

	`submodule` 不能使用 `git pull`、`git fetch` 来同步, 需要使用以下命令:

	```bash
	git submodule init
	git submodule update
	```

	最后, 再执行 `hexo g -d` 命令, 即可将主题文件生成至 `master`.

## 总结

------

### 文章更新流程

```bash
git fetch origin branch/hexo
git diff
git merge --no-ff origin/branch/hexo

# update article...

git add .
git commit -m ":construction: update article named `xxx`"
git push

# send article to website...

hexo g -d
```

## 参考

------

- [Hexo博客多端同步问题](https://juejin.im/post/5af8f087f265da0b886d857a)