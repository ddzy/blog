---
title: vscode深度探索系列之——插件开发总结
date: 2019-01-31 13:55:22
tags: [vscode]
categories: [frontend, freebie]
---

对`vscode`插件开发的过程和踩坑作个记录, 顺便附上[插件地址](https://github.com/ddzy/file-positioning).


<!-- more -->


## 更新

------

### [2019-3-24]

#### Changed

- 剔除`h2`、`h3`等小标题加粗符号

#### Added

- 新增`插件地址`

## 环境部署

------

### 安装

**1. Yeomen**

> 项目生成器

**2. generator-code**

> `vscode`官方的插件开发脚手架

```bash
npm install -g yo generator-code
```

### 生成

> 生成项目结构

```bash
yo code
```

> 更改`git`配置

```bash
// .git/config
[core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
	symlinks = false
	ignorecase = true
[remote "origin"]
	url = https://github.com/xxx/xxxxxx.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
```

### 测试

- F5
- Ctrl + Shift + P
- commandName

## package.json

------

### *命令行绑定*

> 通过`ctrl+shift+p`打开的命令行输入启动命令

```ts
	"activationEvents": [
		"onCommand:extension.helloWorld",
	],
	"contributes": {
		"commands": [
			{
				"command": "extension.helloWorld",
				"title": "Hello World"
			}
		],
	},
```

### ***按键绑定***

> 直接通过`按键`绑定

```ts
	"activationEvents": [
		"onCommand:extension.fileposition"
	],
	"contributes": {
		"keybindings": [
			{
				"key": "Ctrl+F2",
				"command": "extension.fileposition"
			}
		]
	},
```

## 常用API

------

> 按照`namespace`加以区分

### window

#### window.showInformationMessage

> 右下角提示信息

```ts
window.showInfomationMessage(`string`);
```

#### window.createInputBox

> 创建全局输入框

```ts
const input: InputBox = window.createInputBox();
```

#### window.showInputBox

> 展示输入框

```ts
window.showInputBox(input);
```

### *task*

### *env*

### *commands*

### *workspace*

#### workspace.workspaceFolders

> 获取当前编辑器内的所有 `工作区文件夹`

```ts
const folders: vscode.WorkspaceFolder[] | undefined = workspace.workspaceFolders;
```

### *languages*

### *scm*

### *debug*

### *extensions*

## 错误集锦

------

> Error: command failed with error code 2: error parsing glob '!**​/node_modules/**': invalid use of **; must be one path...

**解决办法:** : 在`.vscode`下的`settings.json`中分别对应添加`node_modules`项即可