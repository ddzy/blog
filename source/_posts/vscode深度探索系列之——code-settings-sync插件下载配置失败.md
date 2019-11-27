---
title: vscode深度探索系列之——code_settings_sync插件下载配置失败
date: 2019-09-25 19:29:11
tags: [vscode]
categories: [frontend, freebie]
---

今日在使用 VS Code 的[code-settings-sync](https://github.com/shanalikhan/code-settings-sync)插件做同步时, 发现可以正常上传(`更新`)gist, 但是另一方的 VS Code 无法下载, 这里将处理过程记录下来.


<!-- more -->


## 更新

------

### [2019-9-25]

- Initial release

## 前言

------

`Settings Sync`插件地址:

https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync

## 思考

------

开始以为是开了`代理`的问题, 但是仔细想想, 是可以正常上传的.

随即翻了下该项目的`issue`, 找到了一个相似的问题:

- ['Download' doesn't install missing extensions](https://github.com/shanalikhan/code-settings-sync/issues/978)

大概意思是需要开启`Force Download`这个配置项, 具体步骤参下:

## 解决

------

### 步骤一

<kbd>Ctrl + Shift + P</kbd>打开命令窗口, 输入`sync`:

![open_vscode_command_line](https://oos.blog.yyge.top/2019/9/25/vscode%E6%B7%B1%E5%BA%A6%E6%8E%A2%E7%B4%A2%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94code_settings_sync%E6%8F%92%E4%BB%B6%E4%B8%8B%E8%BD%BD%E9%85%8D%E7%BD%AE%E5%A4%B1%E8%B4%A5/images/open_vscode_command_line.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

### 步骤二

点击`高级选项`, 进入插件设置:

![open_code_settings_config](https://oos.blog.yyge.top/2019/9/25/vscode%E6%B7%B1%E5%BA%A6%E6%8E%A2%E7%B4%A2%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94code_settings_sync%E6%8F%92%E4%BB%B6%E4%B8%8B%E8%BD%BD%E9%85%8D%E7%BD%AE%E5%A4%B1%E8%B4%A5/images/open_code_settings_sync_config.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

点击`开关强制下载`:

![enable_force_download](https://oos.blog.yyge.top/2019/9/25/vscode%E6%B7%B1%E5%BA%A6%E6%8E%A2%E7%B4%A2%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94code_settings_sync%E6%8F%92%E4%BB%B6%E4%B8%8B%E8%BD%BD%E9%85%8D%E7%BD%AE%E5%A4%B1%E8%B4%A5/images/enable_force_download.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

### 步骤三

之后重新点`下载配置`按钮, 即可正常下载.